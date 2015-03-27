<properties
   pageTitle="Verwalten von HDInsight-Clustern mit Ambari | Azure"
   description="Erfahren Sie, wie Sie Ambari zum Überwachen und Verwalten von Linux-basierten HDInsight-Clustern verwenden."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Verwalten von HDInsight-Clustern mit Ambari (Vorschau)

Erfahren Sie, wie Sie Ambari zum Verwalten und Überwachen von Linux-basierten HDInsight-Clustern verwenden.

> [AZURE.NOTE] Ein Großteil der Informationen in diesem Artikel gelten nur für Linux-basierte HDInsight-Cluster. Für Windows-basierte HDInsight-Cluster ist die Überwachung nur über die Ambari-REST-API verfügbar. Weitere Informationen finden Sie unter [Überwachen von Hadoop in HDInsight mit der Ambari-API](../hdinsight-monitor-use-ambari-api/).

## <a id="whatis"></a>Was ist Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> vereinfacht die Hadoop-Verwaltung durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche, die zum Bereitstellen, Verwalten und Überwachen von Hadoop-Clustern verwendet werden kann. Entwickler können diese Funktionen mithilfe der <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari-REST-APIs</a> in ihre Anwendungen integrieren. 

Ambari wird standardmäßig mit Linux-basierten Clustern bereitgestellt. Windows-basierte HDInsight-Cluster bieten Überwachungsfunktionen über die Ambari-REST-APIs.

## SSH-Proxy

> [AZURE.NOTE] Während Ambari für Ihren Cluster direkt über das Internet zugänglich ist, benötigen einige Funktionen den Knotenzugriff über den internen Domänennamen, der vom Cluster verwendet wird. Da dies ein interner und somit nicht öffentlicher Domänenname ist, erhalten Sie entsprechende Fehler, dass der Server nicht gefunden wurde, wenn Sie versuchen, auf einige Features über das Internet zuzugreifen.

Um dieses Problem zu umgehen, verwenden Sie einen SSH-Tunnel, um den Webdatenverkehr per Proxy an den Stammknoten des Clusters weiterzuleiten, der die internen Domänennamen erfolgreich auflösen kann. Verwenden Sie die folgenden Artikel, um von einem Port des lokalen Computers einen SSH-Tunnel zum Cluster einzurichten.

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix, oder OS X</a> - Schritte zum Erstellen eines SSH-Tunnels mit dem `ssh`-Befehl.

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows</a> - Schritte zum Verwenden von PuTTy zum Erstellen eines SSH-Tunnels

## Ambari-Webbenutzeroberfläche

Die Ambari-Webbenutzeroberfläche steht auf jedem Linux-basierten HDInsight-Cluster zur Verfügung, den Sie unter **https://&lt;clustername>.azurehdinsight.net** erstellen. Sie können diese Seite auch über die Schaltfläche **Ambari Web** am unteren Rand des Cluster-Dashboards im Azure-Portal erreichen.

![ambari web icon](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

Sie werden auf der Seite zweimal zur Authentifizierung aufgefordert. Die erste Aufforderung ist für die Authentifizierung am HDInsight-Cluster vorgesehen, während die zweite Aufforderung zur Authentifizierung bei Ambari erfolgt.

* **Clusterauthentifizierung**: verwenden Sie den Benutzernamen (Standardeinstellung ist **admin**) und das Kennwort des Clusteradministrators

* **Ambari-Authentifizierung**: die Standardeinstellung für Benutzername und Kennwort ist **admin**

	> [AZURE.NOTE] Wenn Sie das Kennwort für den Benutzer **admin** geändert haben, müssen Sie das neue Kennwort eingeben.

Wenn die Seite geöffnet wird, beachten Sie die Leiste im oberen Bereich. Sie enthält die folgenden Informationen und Steuerelemente:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari-Logo**: öffnet das **Dashboard**

* **Anzahl der Vorgänge für Clustername**: zeigt die Anzahl der laufenden Ambari-Vorgänge an Wenn Sie den Clusternamen oder die **Anzahl der Vorgänge** auswählen, wird eine Liste der Hintergrundvorgänge angezeigt.

* **Anzahl der Warnungen**: Warnungen oder kritische Warnungen, die ggf. für den Cluster angezeigt werden Wenn Sie diese Option auswählen, wird eine Liste der Warnungen angezeigt.

* **Dashboard**: zeigt das Dashboard an, mit dem der Cluster überwacht werden kann

* **Dienste**: Informationen und Konfigurationseinstellungen für die Dienste auf dem Cluster

* **Hosts**: Informationen und Konfigurationseinstellungen für die Knoten im Cluster

* **Warnungen**: eine Liste von Informationen, Warnungen und kritischen Warnungen

* **Admin**: Softwarestapel/Dienste, die im Cluster installiert sind oder hinzugefügt werden können, Dienstkontoinformationen sowie die Kerberos-Sicherheit

* **Schaltfläche "Admin"**: Ambari-Verwaltung, Benutzereinstellungen und Abmeldung

### Überwachung

#### Warnungen

Ambari bietet viele Warnungen, die eine der folgenden Statusangaben aufweisen:

* **OK**

* **Warnung**

* **KRITISCH**

* **UNBEKANNT**

Andere Warnungen als **OK** führen dazu, dass der Eintrag **Anzahl der Warnungen** am Seitenanfang die Anzahl der Warnungen anzeigt. Wenn Sie diesen Eintrag auswählen, werden die Warnungen und deren Statusangaben angezeigt.

Warnungen sind in mehrere Standardgruppen angeordnet, die über die Seite **Warnungen** angezeigt werden können. 

![alerts page](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Sie können die Gruppen verwalten, indem Sie das Menü **Aktionen** und dann die Option **Warnungsgruppen verwalten** auswählen. Dadurch können Sie vorhandene Gruppen ändern oder neue Gruppen erstellen.

![manage alert groups dialog](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Sie können über das Menü **Aktionen** auch **Warnbenachrichtigungen** erstellen. Dadurch können Sie Auslöser erstellen, die Benachrichtigungen per **E-Mail** oder **SNMP** senden, wenn bestimmte Kombinationen von Warnung und Schweregrad auftreten. Sie können z. B. eine Warnung senden, wenn eine der Benachrichtigungen in der Gruppe **YARN-Standard** auf **Kritisch** eingestellt ist.

![Create alert dialog](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

#### Cluster

Die Registerkarte **Metriken** auf dem **Dashboard** enthält eine Reihe von Widgets, die das Überwachen des Clusterstatus auf einen Blick erleichtern. Verschiedene Widgets wie **CPU-Auslastung** bieten zusätzliche Informationen, wenn Sie darauf klicken.

![dashboard with metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

Die Registerkarte **Wärmebilder** zeigt Metriken als farbige Wärmebilder an, die von Grün bis Rot reichen.

![dashboard with heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Ausführlichere Informationen zu den Knoten im Cluster finden Sie unter **Hosts**. Wählen Sie dort den jeweiligen Knoten, an dem Sie interessiert sind.

![host details](./media/hdinsight-hadoop-manage-ambari/host-details.png)

#### Dienst

Die Seitenleiste **Dienste** des Dashboards bietet einen schnellen Überblick über den Status der Dienste, die auf dem Cluster aktiv sind. Es werden verschiedene Symbole verwendet, um den Status oder auszuführende Aktionen anzuzeigen, z. B. ein gelbes Recycling-Symbol, wenn ein Dienst wiederverwendet werden muss.

![services side-bar](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Wenn Sie einen Dienst auswählen, werden weitere ausführliche Informationen zum Dienst angezeigt.

![service summary information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

##### Quicklinks

Einige Dienste zeigen oben auf der Seite den Link **Quicklinks** an. Hierüber können Sie auf die dienstabhängige Webbenutzeroberfläche zugreifen. Beispiel:

* **Auftragsverlauf**: MapReduce-Auftragsverlauf

* **Ressourcen-Manager**: Benutzeroberfläche des YARN-Ressourcen-Managers

* **NameNode**: HDFS-NameNode-Benutzeroberfläche

* **Oozie-Webbenutzeroberfläche**: Oozie-Benutzeroberfläche

Wenn Sie einen dieser Links öffnen, wird eine Registerkarte im Browser geöffnet, auf der die ausgewählte Seite angezeigt wird.

> [AZURE.NOTE] Wenn Sie einen **Quicklink** für einen Dienst auswählen, führt dies zum Fehler "Server nicht gefunden", sofern Sie nicht einen SSL-Tunnel verwenden, um den Webdatenverkehr per Proxy an den Cluster weiterzuleiten. Dies liegt darin begründet, dass Ambari den internen Domänennamen für diese Links verwendet.
> 
> Informationen zur Verwendung eines SSL-Tunnels mit HDInsight finden Sie in einem der folgenden Themen.
> 
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix, oder OS X</a> - Schritte zum Erstellen eines SSH-Tunnels mit dem `ssh`-Befehl.
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows</a> - Schritte zum Verwenden von Putty zum Erstellen eines SSH-Tunnels

### Verwaltung

#### Ambari-Benutzer, -Gruppen und -Berechtigungen

Während der Linux-basierten HDInsight-Vorschau sollten keine Benutzer, Gruppen und Berechtigungen verwaltet werden.

#### Host

Die Seite **Hosts** listet alle Hosts im Cluster auf. Gehen Sie folgendermaßen vor, um Hosts zu verwalten.

![hosts page](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] Für HDInsight-Cluster sollte kein Host hinzugefügt, zurückgesetzt oder wiederhergestellt werden.

1. Wählen Sie die Hosts aus, die Sie verwalten möchten.

2. Verwenden Sie das Menü **Aktionen**, um die Aktion auswählen, die Sie durchführen möchten.

	* **Alle Komponenten starten**: alle Komponenten auf dem Host starten

	* **Alle Komponenten beenden**: alle Komponenten auf dem Host beenden

	* **Alle Komponenten neu starten**: alle Komponenten auf dem Host neu starten

	* **Wartungsmodus aktivieren**: unterdrückt Warnungen für den Host Diese Option sollte aktiviert werden, wenn Sie Aktionen ausführen, die Warnungen generieren. Beispielsweise der Neustart eines Diensts, von dem aktive Dienste abhängig sind.

	* **Wartungsmodus deaktivieren**: versetzt den Host wieder in den normalen Warnmodus

	* **Beenden**: beendet "DataNode" oder "NodeManager" auf dem Host

	* **Starten**: startet "DataNode" oder "NodeManager" auf dem Host

	* **Neu starten**: beendet "DataNode" oder "NodeManager" auf dem Host und startet sie dann neu

	* **Außerbetriebsetzung**: entfernt einen Host aus dem Cluster

		> [AZURE.NOTE] Verwenden Sie diese Aktion nicht für HDInsight-Cluster.

	* **Wiederherstellung**: fügt einen zuvor außer Betrieb gesetzten Host zum Cluster hinzu

		> [AZURE.NOTE] Verwenden Sie diese Aktion nicht für HDInsight-Cluster.

#### <a id="service"></a>Dienst

Verwenden Sie auf der Seite **Dashboard** oder **Dienste** am Ende der Dienstliste die Schaltfläche **Aktionen**, um neue Dienste **hinzuzufügen** oder alle Dienste zu **beenden** und zu **starten**.

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

Nachfolgend sind die allgemeinen Schritte zum **Hinzufügen eines Diensts** aufgeführt:

1. Verwenden Sie auf der Seite **Dashboard** oder **Dienste** die Schaltfläche **Aktionen**, und wählen Sie **Dienst hinzufügen** aus.

2. Wählen Sie im **Assistenten zum Hinzufügen von Diensten** den hinzuzufügenden Dienst aus, und klicken Sie auf **Weiter**.

	![add service](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. Fahren Sie mit dem Assistenten fort, und stellen Sie die Konfigurationsinformationen für den Dienst bereit. Weitere Informationen zu Konfigurationsanforderungen finden Sie in der Dokumentation zum jeweiligen Dienst, den Sie hinzufügen.

4. Auf der Seite **Überprüfung** können Sie die Konfigurationsinformationen **drucken** oder den Dienst für den Cluster **bereitstellen**.

5. Nachdem der Dienst bereitgestellt wurde, zeigt die Seite **Installieren, Starten und Testen** Statusinformationen an, während der Dienst installiert und getestet wird. Sobald der **Status** grün ist, wählen Sie **Weiter** aus.

	![image of install, start, and test page](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. Die Seite **Zusammenfassung** zeigt eine Zusammenfassung des Installationsvorgangs sowie alle möglichen Aktionen an, die Sie ausführen müssen. Beispielsweise den Neustart anderer Dienste. Wählen Sie **Fertig stellen**, um den Assistenten zu beenden.

Während die Schaltfläche **Aktionen** alle Dienste neu starten kann, ist es möglicherweise häufig erforderlich, dass Sie bestimmte Dienste starten, beenden oder neu starten möchten. Gehen Sie wie folgt vor, um **Aktionen für einzelne Dienste auszuführen**:

1. Wählen Sie auf der Seite **Dashboard** oder **Dienste** einen Dienst aus.

2. Verwenden Sie am oberen Rand der Registerkarte **Zusammenfassung** die Schaltfläche **Dienstaktionen**, und wählen Sie dann die auszuführende Aktion aus. Dadurch wird der Dienst auf allen Knoten neu gestartet.

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] Der Neustart einiger Dienste bei aktivem Cluster kann dazu führen, dass Warnungen generiert werden. Um dies zu vermeiden, können Sie die Schaltfläche **Dienstaktionen** verwenden, um den **Wartungsmodus** für den Dienst zu aktivieren, bevor Sie den Neustart durchführen.

3. Nachdem eine Aktion ausgewählt wurde, wird der Eintrag **Anzahl der Vorgänge** am Seitenanfang erhöht, um anzuzeigen, dass ein Hintergrundvorgang ausgeführt wird. Sofern die Anzeige konfiguriert ist, wird die Liste der Hintergrundvorgänge angezeigt.

	> [AZURE.NOTE] Wenn Sie den **Wartungsmodus** für den Dienst aktiviert haben, denken Sie daran, ihn mithilfe der Schaltfläche **Dienstaktionen** wieder zu deaktivieren, sobald der Vorgang abgeschlossen ist.

Gehen Sie wie folgt vor, um einen **Dienst zu konfigurieren**:

1. Wählen Sie auf der Seite **Dashboard** oder **Dienste** einen Dienst aus.

2. Wählen Sie die Registerkarte **Konfigurationen** aus. Daraufhin wird die aktuelle Konfiguration angezeigt. Zudem wird eine Liste der vorherigen Konfigurationen angezeigt.

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Verwenden Sie die angezeigten Felder, um die Konfiguration zu ändern, und wählen Sie dann **Speichern** aus. Sie können auch eine vorherige Konfiguration und dann **Als aktuell festlegen** auswählen, um ein Rollback zu den vorherigen Einstellungen durchzuführen.

## REST-API

Ambari Web basiert auf einer zugrunde liegenden REST-API, die Sie zum Erstellen Ihrer eigenen Verwaltungs- und Überwachungstools nutzen können. Obwohl die API verhältnismäßig einfach zu verwenden ist, müssen für Azure einige Besonderheiten beachtet werden.

* **Authentifizierung**: Benutzername des Clusteradministrators (Standardmäßig **admin**)
*  und Kennwort sollte zur Authentifizierung für den Dienst verwendet werden.

* **Sicherheit**: Ambari verwendet die Standardauthentifizierung, daher sollten Sie für die Kommunikation mit der API immer HTTPS verwenden.

* **IP-Adressen**: Auf die für Hosts in einem Cluster zurückgegebene Adresse kann von außerhalb des Clusters nicht zugegriffen werden, es sei denn, der Cluster ist Mitglied eines Azure Virtual Network. Dann können andere Mitglieder des virtuellen Netzwerks auf die IP-Adresse zugreifen, jedoch nicht von außerhalb des Netzwerks.

* **Einige Funktionen sind nicht aktiviert**: Einige Ambari-Funktionen sind deaktiviert, da sie vom HDInsight-Cloud-Dienst verwaltet werden. Beispielsweise ist das Hinzufügen von Hosts zum Cluster oder das Entfernen von Hosts aus dem Cluster deaktiviert. Andere Funktionen sind möglicherweise nicht vollständig in der Vorschau des Linux-basierten HDInsight implementiert.

Eine vollständige Referenz der REST-API finden Sie unter [Referenz zur Ambari-API V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).


<!--HONumber=47-->
