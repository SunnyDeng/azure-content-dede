<properties
	pageTitle="Verwenden von Hadoop Oozie in HDInsight | Microsoft Azure"
	description="Verwenden von Hadoop Oozie in HDInsight, einer Big Data-Lösung. Erfahren Sie, wie Sie einen Oozie-Workflow definieren und einen Oozie-Auftrag übermitteln können."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="larryfr"/>


# Verwenden von Oozie mit Hadoop zum Definieren und Ausführen eines Workflows in Linux-basiertem HDInsight \(Vorschau\)

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Informationen zum Verwenden von Apache Oozie zum Definieren eines Workflows, der Hive und Sqoop verwendet, und anschließenden Ausführen des Workflows in einem Linux-basierten HDInsight-Cluster.

Apache Oozie ist ein Workflow-/Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stapel integriert und unterstützt Hadoop-Aufträge für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Oozie kann auch dazu verwendet werden, bestimmte Aufträge für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

> [AZURE.NOTE]Eine weitere Option zum Definieren von Workflows mit HDInsight ist Azure Data Factory. Weitere Informationen zu Azure Data Factory finden Sie unter [Verwenden von Pig und Hive mit Data Factory][azure-data-factory-pig-hive].

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**: Siehe [Kostenlose Azure-Testversion](get-azure-free-trial-for-testing-hadoop-in-hdinsight.md).

- **Azure-Befehlszeilenschnittstelle**: Weitere Informationen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](xplat-cli.md).

- **Einen HDInsight-Cluster**: Siehe [Erste Schritte mit HDInsight unter Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Eine Azure SQL-Datenbank**: Wird anhand der in diesem Dokument beschriebenen Schritte erstellt.

##Beispielworkflow

Der Workflow, den Sie mithilfe der Anweisungen in diesem Dokument implementieren, enthält zwei Aktionen. Aktionen sind Definitionen von Aufgaben, z. B. das Ausführen von Hive, Sqoop, MapReduce oder anderen Prozessen:

![Workflowdiagramm][img-workflow-diagram]

1. Eine Hive-Aktion führt ein HiveQL-Skript zum Extrahieren von Datensätzen aus der in HDInsight enthaltenen Tabelle **hivesampletable** aus. Jede Datenzeile beschreibt einen Besuch eines bestimmten Mobilgeräts. Das Format des Eintrags sieht wie folgt aus:

		8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
		23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
		23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

	Das in diesem Dokument verwendete Hive-Skript zählt die gesamten Besuche für jede Plattform \(z. B. Android oder iPhone\) und speichert die Werte in einer neuen Hive-Tabelle.

	Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

2.  Die Sqoop-Aktion exportiert den Inhalt der neuen Hive-Tabelle in eine Tabelle in einer Azure SQL-Datenbank. Weitere Informationen über Sqoop finden Sie unter [Verwenden von Hadoop Sqoop mit HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE]Informationen zu den unterstützten Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][hdinsight-versions].

##Erstellen des Arbeitsverzeichnisses

Oozie erwartet, dass die für einen Auftrag erforderlichen Ressourcen im selben Verzeichnis gespeichert werden. In diesem Beispiel wird **wasb:///tutorials/useoozie** verwendet. Geben Sie den folgenden Befehl zum Erstellen dieses Verzeichnisses und des Datenverzeichnisses ein, das die mit diesem Workflow erstellte neue Hive-Tabelle enthalten wird:

	hadoop fs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE]Der `-p`-Parameter bewirkt, dass alle Verzeichnisse im Pfad erstellt werden, sofern sie nicht bereits vorhanden sind. Das Verzeichnis **Data** dient zum Speichern von Daten, die vom Skript **useooziewf.hql** verwendet werden.

Führen Sie auch den folgenden Befehl aus, der sicherstellt, dass Oozie die Identität Ihres Benutzerkontos annehmen kann, wenn Hive- und Sqoop-Aufträge ausgeführt werden. Ersetzen Sie **USERNAME** durch Ihren Benutzernamen:

	sudo adduser USERNAME users

Wenn Sie die Fehlermeldung erhalten, dass der Benutzer bereits zu den Benutzern gehört, können Sie diese einfach ignorieren.

##Hinzufügen eines Datenbanktreibers

Da dieser Workflow Sqoop zum Exportieren von Daten in die SQL-Datenbank verwendet, müssen Sie eine Kopie des JDBC-Treibers bereitstellen, der für die Kommunikation mit der SQL-Datenbank genutzt wird. Verwenden Sie den folgenden Befehl, um ihn in das Arbeitsverzeichnis zu kopieren:

	hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /tutorials/useoozie/sqljdbc4.jar

Wenn Ihr Workflow andere Ressourcen, wie z. B. eine JAR-Datei mit einer MapReduce-Anwendung, verwendet hat, müssen Sie diese auch hinzufügen.

##Definieren der Hive-Abfrage

Führen Sie die folgenden Schritte aus, um ein HiveQL-Skript zu erstellen, das eine Abfrage definiert, die weiter unten in diesem Dokument in einem Oozie-Workflow verwendet wird.

1. Stellen Sie über SSH eine Verbindung mit dem Linux-basierten HDInsight-Cluster her:

    * **Linux-, Unix- oder OS X-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Verwenden Sie den folgenden Befehl, um eine neue Datei zu erstellen:

        nano useooziewf.hql

1. Verwenden Sie, sobald der Nano-Editor geöffnet ist, Folgendes als Inhalt der Datei:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
		FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

	Im Skript werden zwei Variablen verwendet:

	- **${HiveTableName}**: Enthält den Namen der zu erstellenden Tabelle
	- **${HiveDataFolder}**: Enthält den Speicherort der Datendateien für die Tabelle

	Die Workflowdefinitionsdatei \("workflow.xml" in diesem Tutorial\) übergibt diese Werte zur Laufzeit an das HiveQL-Skript.

2. Drücken Sie STRG+X, um den Editor zu verlassen. Wählen Sie bei Aufforderung **Y** aus, um die Datei zu speichern. Drücken Sie dann die **EINGABETASTE**, um den Dateinamen **useooziewf.hql** zu verwenden.

3. Verwenden Sie die folgenden Befehle zum Kopieren von **useooziewf.hql** in **wasb:///tutorials/useoozie/useooziewf.hql**:

		hadoop fs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

	Diese Befehle dienen zum Speichern der Datei **useooziewf.hql** im Azure Storage-Konto, das diesem Cluster zugeordnet ist, wodurch die Datei erhalten bleibt, selbst wenn der Cluster gelöscht wird. Dadurch können Sie Kosten sparen, indem Sie Cluster löschen, wenn sie nicht verwendet werden, während gleichzeitig Ihre Aufträge und Workflows erhalten bleiben.

##Definieren des Workflows

Definitionen von Oozie-Workflows werden in hPDL \(einer XML-Prozessdefinitionssprache\) geschrieben. Führen Sie zum Definieren des Workflows die folgenden Schritte aus:

1. Verwenden Sie die folgende Anweisung zum Erstellen und Bearbeiten einer neuen Datei:

        nano workflow.xml

1. Sobald der Nano-Editor geöffnet ist, geben Sie Folgendes als Inhalt der Datei ein:

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		  <start to = "RunHiveScript"/>
		  <action name="RunHiveScript">
		    <hive xmlns="uri:oozie:hive-action:0.2">
		      <job-tracker>${jobTracker}</job-tracker>
		      <name-node>${nameNode}</name-node>
		      <configuration>
		        <property>
		          <name>mapred.job.queue.name</name>
		          <value>${queueName}</value>
		        </property>
		      </configuration>
		      <script>${hiveScript}</script>
		      <param>hiveTableName=${hiveTableName}</param>
		      <param>hiveDataFolder=${hiveDataFolder}</param>
		    </hive>
		    <ok to="RunSqoopExport"/>
		    <error to="fail"/>
		  </action>
		  <action name="RunSqoopExport">
		    <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		      <job-tracker>${jobTracker}</job-tracker>
		      <name-node>${nameNode}</name-node>
		      <configuration>
		        <property>
		          <name>mapred.compress.map.output</name>
		          <value>true</value>
		        </property>
		      </configuration>
		      <arg>export</arg>
		      <arg>--connect</arg>
		      <arg>${sqlDatabaseConnectionString}</arg>
		      <arg>--table</arg>
		      <arg>${sqlDatabaseTableName}</arg>
		      <arg>--export-dir</arg>
		      <arg>${hiveDataFolder}</arg>
		      <arg>-m</arg>
		      <arg>1</arg>
		      <arg>--input-fields-terminated-by</arg>
		      <arg>"\t"</arg>
		      <archive>sqljdbc4.jar</archive>
		      </sqoop>
		    <ok to="end"/>
		    <error to="fail"/>
		  </action>
		  <kill name="fail">
		    <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		  </kill>
		  <end name="end"/>
		</workflow-app>

	Im Workflow sind zwei Aktionen definiert:

	- **RunHiveScript**: Dies ist die Startaktion, die das Hive-Skript **useooziewf.hql** ausführt.

	- **RunSqoopExport**: Diese Aktion exportiert die vom Hive-Skript erstellten Daten mithilfe von Sqoop in die SQL-Datenbank. Diese Aktion wird nur ausgeführt, wenn die Aktion **RunHiveScript** erfolgreich war.

		> [AZURE.NOTE]Weitere Informationen über den Oozie-Workflow und die Verwendung von Workflowaktionen finden Sie in der [Apache Oozie 4.0-Dokumentation][apache-oozie-400] \(für HDInsight der Version 3.0\) oder in der [Apache Oozie 3.3.2-Dokumentation][apache-oozie-332] \(für HDInsight der Version 2.1\).

	Beachten Sie, dass der Workflow mehrere Einträge hat, z. B. `${jobTracker}`, die durch Werte ersetzt werden, die Sie in der Auftragsdefinition weiter unten in diesem Dokument verwenden.

	Beachten Sie auch den Eintrag `<archive>sqljdbc4.jar</arcive>` im Abschnitt "Sqoop". Dieser weist Oozie an, dieses Archiv für Sqoop zur Verfügung zu stellen, wenn diese Aktion ausgeführt wird.

2. Drücken Sie STRG+X, dann **Y** und die **EINGABETASTE** zum Speichern der Datei.

3. Verwenden Sie den folgenden Befehl zum Kopieren der Datei **workflow.xml** in **wasb:///tutorials/useoozie/workflow.xml**:

		hadoop fs -copyFromLocal workflow.xml wasb:///tutorials/useoozie/workflow.xml

##Erstellen der Datenbank

Die folgenden Schritte dienen zum Erstellen der Azure SQL-Datenbank, in die Daten exportiert werden sollen.

> [AZURE.IMPORTANT]Vor dem Ausführen dieser Schritte müssen Sie [die Azure-Befehlszeilenschnittstelle installieren und konfigurieren](xplat-cli.md). Das Installieren der Befehlszeilenschnittstelle und die Schritte zum Erstellen einer Datenbank können entweder im HDInsight-Cluster oder auf der lokalen Arbeitsstation ausgeführt werden.

1. Verwenden Sie den folgenden Befehl, um einen neuen Azure SQL-Datenbankserver zu erstellen:

        azure sql server create <adminLogin> <adminPassword> <region>

    Beispiel: `azure sql server create admin password "West US"`.

    Wenn der Befehl abgeschlossen ist, erhalten Sie eine Antwort ähnlich der folgenden:

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT]Beachten Sie den von diesem Befehl zurückgegebenen Servernamen \("i1qwc540ts" im obigen Beispiel.\) Dies ist der Kurzname des erstellten SQL-Datenbankservers. Der vollqualifizierte Domänenname \(FQDN\) ist **&lt;Kurzname&gt;.database.windows.net**. Für das obige Beispiel lautet der FQDN **i1qwc540ts.database.windows.net**.

2. Verwenden Sie den folgenden Befehl zum Erstellen einer Datenbank mit dem Namen **oozietest** auf dem SQL-Datenbankserver:

        azure sql db create [options] <serverName> oozietest <adminLogin> <adminPassword>

    Nach seinem Abschluss wird die Meldung "OK" zurückgegeben.

	> [AZURE.NOTE]Wenn Sie eine Fehlermeldung über fehlenden Zugriff erhalten, müssen Sie die IP-Adresse des Systems der Firewall der SQL-Datenbank mit dem folgenden Befehl hinzufügen:
    >
    > `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

###Erstellen der Tabelle

> [AZURE.NOTE]Es gibt viele Möglichkeiten, zum Erstellen einer Tabelle eine Verbindung mit SQL Database herzustellen. Die folgenden Schritte verwenden [FreeTDS](http://www.freetds.org/) aus dem HDInsight-Cluster.

3. Verwenden Sie den folgenden Befehl, um FreeTDS im HDInsight-Cluster zu installieren:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Sobald FreeTDS installiert wurde, verwenden Sie folgenden Befehl, um sich mit dem zuvor erstellten SQL-Datenbank-Server zu verbinden.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    Eine Ausgabe ähnlich der folgenden wird angezeigt:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

		CREATE TABLE [dbo].[mobiledata](
		[deviceplatform] [nvarchar](50),
		[count] [bigint])
		GO
		CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
		GO

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Dadurch wird eine neue Tabelle namens **mobiledata** erstellt, in die von Sqoop Daten geschrieben werden.

    Stellen Sie wie folgt sicher, dass die Tabelle erstellt wurde:

        SELECT * FROM information_schema.tables
        GO

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. Geben Sie zum Beenden des Dienstprogramms tsql an der Eingabeaufforderung `1>` die Zeichenfolge `exit` ein.

##Erstellen der Auftragsdefinition

Die Auftragsdefinition beschreibt den Speicherort der Datei "workflow.xml" sowie anderer Dateien, die vom Workflow verwendet werden \(Beispiel: useooziewf.hql\). Sie definiert auch die Werte für Eigenschaften, die innerhalb des Workflows verwendet werden, und zugeordnete Dateien.

1. Geben Sie den folgenden Befehl ein, um die vollständige WASB-Adresse des Standardspeichers abzurufen. Diese wird in Kürze weiter unten in der Konfigurationsdatei verwendet:

		sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

	Die Ausgabe dieses Befehls sollte etwa so aussehen:

		<name>fs.defaultFS</name>
		<value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>

	Speichern Sie den Wert **wasb://mycontainer@mystorageaccount.blob.core.windows.net**, da er in den nächsten Schritten verwendet wird.

2. Rufen Sie den folgenden Befehl auf, um den FQDN des Cluster-Hauptknotens abzurufen. Dieser wird für die JobTracker-Adresse des Clusters verwendet. Diese wird in Kürze weiter unten in der Konfigurationsdatei verwendet:

		hostname -f

	Die Ausgabe dieses Befehls sieht etwa so aus:

		headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net

	Der für den JobTracker verwendete Port ist 8050, weshalb die vollständige Adresse für den JobTracker **headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:8050** lautet.

1. Verwenden Sie zum Erstellen der Konfiguration der Oozie-Auftragsdefinition Folgendes:

		nano job.xml

2. Verwenden Sie, sobald der Nano-Editor geöffnet ist, Folgendes als Inhalt der Datei:

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		  <property>
		    <name>nameNode</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
		  </property>

		  <property>
		    <name>jobTracker</name>
		    <value>JOBTRACKERADDRESS</value>
		  </property>

		  <property>
		    <name>queueName</name>
		    <value>default</value>
		  </property>

		  <property>
		    <name>oozie.use.system.libpath</name>
		    <value>true</value>
		  </property>

		  <property>
		    <name>hiveScript</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
		  </property>

		  <property>
		    <name>hiveTableName</name>
		    <value>mobilecount</value>
		  </property>

		  <property>
		    <name>hiveDataFolder</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
		  </property>

		  <property>
		    <name>sqlDatabaseConnectionString</name>
		    <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
		  </property>

		  <property>
		    <name>sqlDatabaseTableName</name>
		    <value>mobiledata</value>
		  </property>

		  <property>
		    <name>user.name</name>
		    <value>YourName</value>
		  </property>

		  <property>
		    <name>oozie.wf.application.path</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		  </property>
		</configuration>

	* Ersetzen Sie alle Vorkommen von **wasb://mycontainer@mystorageaccount.blob.core.windows.net** durch den Wert, den Sie zuvor erhalten haben. 
	> [AZURE.WARNING]Sie müssen den vollständigen WASB-Pfad mit dem Container und Speicherkonto als Teil des Pfads verwenden. Das Verwenden des Kurzformats \(wasb:///\) bewirkt, dass die Aktion "RunHiveScript" misslingt, wenn der Auftrag gestartet wird.

	* Ersetzen Sie **JOBTRACKERADDRESS** durch die JobTracker/ResourceManager-Adresse, die Sie zuvor erhalten haben.

	* Ersetzen Sie **YourName** durch Ihren Anmeldenamen für den HDInsight-Cluster.

	* Ersetzen Sie **serverName**, **adminLogin** und **adminPassword** durch die Informationen für Ihre Azure SQL-Datenbank.

	Die meisten der Informationen in dieser Datei dienen zum Auffüllen der Werte, die in der Datei "workflow.xml" oder "ooziewf.hql" \(z. B. ${NameNode}\) verwendet werden.

	> [AZURE.NOTE]Der Eintrag **oozie.wf.application.path** definiert den Speicherort der Datei "workflow.xml", die den von diesem Auftrag ausgeführten Workflow enthält.

2. Drücken Sie STRG+X, dann **Y** und die **EINGABETASTE** zum Speichern der Datei.

##Übermitteln und Verwalten des Auftrags

Die folgenden Schritte verwenden den Oozie-Befehl zum Übermitteln und Verwalten von Oozie-Workflows im Cluster. Der Oozie-Befehl ist eine benutzerfreundliche Schnittstelle, die über die [Oozie-REST-API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html) zur Verfügung steht.

> [AZURE.IMPORTANT]Wenn Sie den Oozie-Befehl verwenden, müssen Sie den vollqualifizierten Domänennamen \(FQDN\) des HDInsight-Hauptknotens verwenden. Auf diesen FQDN kann nur innerhalb des Clusters zugegriffen werden. Wenn der Cluster sich in einem virtuellen Azure-Netzwerk befindet, ist der Zugriff von anderen Computern im selben Netzwerk aus möglich.

1. Geben Sie zum Abrufen der URL des Oozie-Diensts Folgendes an:

		sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

	Die Ausgabe dieses Befehls sieht etwa so aus:

		<name>oozie.base.url</name>
		<value>http://headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:11000/oozie</value>

	Der Teil **http://headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:11000/oozie** ist die mit dem Oozie-Befehl zu verwendende URL.

2. Geben Sie Folgendes an, um eine Umgebungsvariable für die URL erstellen, damit Sie sie nicht für jeden Befehl eingeben müssen:

		export OOZIE_URL=http://headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:11000/oozie

	Ersetzen Sie die URL durch diejenige, die Sie zuvor erhalten haben.

3. Geben Sie den folgenden Befehl an, um den Auftrag zu übermitteln:

		oozie job -config job.xml -submit

	Dadurch werden die Auftragsinformationen aus der Datei **job.xml** geladen und an Oozie übermittelt, ohne dass die Datei ausgeführt wird.

	Nach Abschluss des Befehls sollte die ID des Auftrags zurückgegeben werden. Beispiel: `0000005-150622124850154-oozie-oozi-W`. Diese wird verwendet, um den Auftrag zu verwalten.

4. Zeigen Sie den Status des Auftrags mit dem folgenden Befehl an. Geben Sie die vom vorherigen Befehl zurückgegebene Auftrags-ID ein:

		oozie job -info <JOBID>

	Die Ausgabe dieses Befehls sieht etwa so aus:

		Job ID : 0000005-150622124850154-oozie-oozi-W
		------------------------------------------------------------------------------------------------------------------------------------
		Workflow Name : useooziewf
		App Path      : wasb:///tutorials/useoozie
		Status        : PREP
		Run           : 0
		User          : USERNAME
		Group         : -
		Created       : 2015-06-22 15:06 GMT
		Started       : -
		Last Modified : 2015-06-22 15:06 GMT
		Ended         : -
		CoordAction ID: -
		------------------------------------------------------------------------------------------------------------------------------------

	Dieser Auftrag hat den Status `PREP`, was bedeutet, dass er übermittelt, aber noch nicht gestartet wurde.

4. Starten Sie den Auftrag mit folgendem Befehl:

		oozie job -start JOBID

	Wenn Sie nach diesem Befehl den Status überprüfen, lautet dieser "Wird ausgeführt", und Informationen für die Aktionen innerhalb des Auftrags werden zurückgegeben.

5. Sobald die Aufgabe erfolgreich abgeschlossen wurde, können Sie mit den folgenden Befehlen überprüfen, ob die Daten generiert und die SQL-Datenbanktabelle exportiert wurden:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

	Geben Sie an der `1>`-Eingabeaufforderung Folgendes ein:

		SELECT * FROM mobiledata
		GO

	Die angezeigten Informationen sehen etwa wie folgt aus:

		deviceplatform  count
		Android 31591
		iPhone OS       22731
		proprietary development 3
		RIM OS  3464
		Unknown 213
		Windows Phone   1791
		(6 rows affected)

Weitere Informationen zum Oozie-Befehl finden Sie unter [Oozie-Befehlszeilentool](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

##Oozie-REST-API

Mit der Oozie-REST-API können Sie eigene Tools erstellen, die mit Oozie arbeiten. Es folgen HDInsight-spezifische Informationen zum Verwenden der Oozie-REST-API:

* **URI**: Auf die REST-API kann von außerhalb des Clusters unter `https://CLUSTERNAME.azurehdinsight.net/oozie` zugegriffen werden.

* **Authentifizierung**: Sie müssen sich bei der API mit dem HTTP-Clusterkonto \(admin\) und Kennwort authentifizieren. Beispiel:

		curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Weitere Informationen zur Verwendung der Oozie-REST-API finden Sie unter [Oozie-Webdienste-API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

##Oozie-Webbenutzeroberfläche

Die Oozie-Webbenutzeroberfläche bietet eine webbasierte Anzeige des Status von Oozie-Aufträgen im Cluster. Sie können den Auftragsstatus, die Auftragsdefinition, die Konfiguration, ein Diagramm mit den Aktionen im Auftrag und Protokolle für den Auftrag anzeigen. Sie können auch ausführliche Informationen zu Aktionen innerhalb eines Auftrags anzeigen.

Um auf die Oozie-Webbenutzeroberfläche zuzugreifen, gehen Sie folgendermaßen vor:

1. Erstellen Sie einen SSH-Tunnel zum HDInsight-Cluster. Informationen hierzu finden in einem der folgenden Artikel:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md#tunnel)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)

2. Nachdem der Tunnel erstellt wurde, öffnen Sie die Ambari-Webbenutzeroberfläche in Ihrem Webbrowser. Der URI der Ambari-Website lautet **https://CLUSTERNAME.azurehdinsight.net**. Ersetzen Sie **CLUSTERNAME** durch den Namen des Linux-basierten HDInsight-Clusters.

3. Wählen Sie auf der linken Seite der Seite **Oozie**, dann **Quicklinks** und schließlich **Oozie Web UI** aus.

	![Abbildung der Menüs](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Die Oozie-Webbenutzeroberfläche zeigt standardmäßig aktive Workflowaufträge an. Um alle Workflowaufträge anzuzeigen, wählen Sie **All Jobs** aus.

	![Alle angezeigten Aufträge](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Wählen Sie einen Auftrag aus, um weitere Informationen dazu anzuzeigen.

	![Auftragsinformationen](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Auf der Registerkarte "Auftragsinformationen" können Sie grundlegende Auftragsinformationen sowie die einzelnen Aktionen innerhalb des Auftrags anzeigen. Auf den Registerkarten am oberen Rand können Sie die Auftragsdefinition und Auftragskonfiguration anzeigen, auf das Auftragsprotokoll zugreifen oder einen gerichteten azyklischen Graph \(Directed Acyclic Graph, DAG\) des Auftrags anzeigen.

	* **Auftragsprotokoll**: Klicken Sie auf die Schaltfläche **GetLogs**, um alle Protokolle für den Auftrag abzurufen, oder verwenden Sie das Feld **Suchfilter eingeben** zum Filtern von Protokollen.

		![Auftragsprotokoll](./media/hdinsight-use-oozie-linux-mac/joblog.png)

	* **JobDAG**: Der DAG ist eine grafische Übersicht über die Datenpfade, die im Workflow gewählt wurden.

		![DAG des Auftrags](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Bei Auswählen einer der Aktionen auf der Registerkarte **Auftragsinformationen** werden Informationen zur Aktion eingeblendet. Wählen Sie z. B. die Aktion **RunHiveScript** aus.

	![Aktionsinformationen](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Sie können Details zur Aktion anzeigen, einschließlich eines Links zur **Konsolen-URL**, die zum Anzeigen von JobTracker-Informationen für den Auftrag dient.

##Planen von Aufträgen

Der Koordinator ermöglicht Ihnen das Angeben des Starts, Endes und der Häufigkeit von Aufträgen, damit sie für bestimmte Zeiten geplant werden können.

Um einen Zeitplan für den Workflow zu definieren, führen Sie die folgenden Schritte aus:

1. Verwenden Sie den folgenden Befehl, um eine neue Datei namens **coordinator.xml** zu erstellen:

		nano coordinator.xml

	Fügen Sie Folgendes als Inhalt der Datei hinzu:

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
	      <action>
	        <workflow>
	          <app-path>${workflowPath}</app-path>
	        </workflow>
	      </action>
		</coordinator-app>

	Hierbei werden Variablen vom Typ `${...}` verwendet, die durch Werte in der Auftragsdefinition ersetzt werden. Die Variablen heißen wie folgt:

	* **${CoordFrequency}**: Zeit zwischen ausgeführten Instanzen des Auftrags
	* **${coordStart}**: Startzeit des Auftrags
	* **${CoordEnd}**: Endzeit des Auftrags
	* **${coordTimezone}**: Koordinatoraufträge erfolgen in einer festen Zeitzone ohne Sommerzeit \(in der Regel unter Verwendung von UTC dargestellt\). Diese Zeitzone wird als "Oozie-Verarbeitungszeitzone" bezeichnet.
	* **${wfPath}**: Pfad der Datei "workflow.xml".

2. Drücken Sie STRG+X, dann **Y** und die **EINGABETASTE** zum Speichern der Datei.

3. Verwenden Sie den folgenden Befehl, um ihn in das Arbeitsverzeichnis dieses Auftrags zu kopieren:

		hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. Verwenden Sie den folgenden Befehl zum Ändern der Datei **job.xml**:

		nano job.xml

	Nehmen Sie die folgenden Änderungen vor:

	* Ändern Sie `<name>oozie.wf.application.path</name>` in `<name>oozie.coord.application.path</name>`. Dies weist Oozie an, die Koordinatordatei statt der Workflowdatei auszuführen.

	* Fügen Sie folgenden Code hinzu, mit dem eine Variable in der Datei "coordinator.xml" so festgelegt wird, dass sie auf den Speicherort der Datei "workflow.xml" verweist:

		    <property>
		      <name>workflowPath</name>
		      <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		    </property>

		Ersetzen Sie die Werte für **mycontainer** und **mystorageaccount** durch die Werte in anderen Einträgen der Datei "job.xml".

	* Fügen Sie die folgenden Code hinzu, mit dem Start, Ende und Häufigkeit für die Datei "coordinator.xml" definiert werden:

		    <property>
		      <name>coordStart</name>
		      <value>2015-06-25T12:00Z</value>
		    </property>

		    <property>
		      <name>coordEnd</name>
		      <value>2015-06-27T12:00Z</value>
		    </property>

		    <property>
		      <name>coordFrequency</name>
		      <value>1440</value>
		    </property>

		    <property>
		      <name>coordTimezone</name>
		      <value>UTC</value>
		    </property>

		Die Startzeit wird auf 12:00 Uhr am 25. Juni 2015, die Endzeit auf den 27. Juni 2015 und das Intervall für die Ausführung dieses Auftrags auf täglich festgelegt \(die Frequenz wird in Minuten angegeben: 24 Stunden x 60 Minuten = 1440 Minuten\). Schließlich wird die Zeitzone auf UTC festgelegt.

5. Drücken Sie STRG+X, dann **Y** und die **EINGABETASTE** zum Speichern der Datei.

6. Um den Auftrag auszuführen, geben Sie den folgenden Befehl ein:

		oozie job -config job.xml -run

	Dadurch wird der Auftrag übermittelt und gestartet.

7. Wenn Sie die Oozie-Webbenutzeroberfläche besuchen und die Registerkarte **Coordinator Jobs** auswählen, sollten die folgenden Informationen angezeigt werden:

	![Registerkarte "Koordinatoraufträge"](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

	Beachten Sie den Eintrag **Next Materialization**, der angibt, wann der Auftrag das nächste Mal ausgeführt wird.

8. Ähnlich wie beim vorherigen Workflowauftrag werden beim Auswählen des Auftragseintrags auf der Webbenutzeroberfläche Informationen zum Auftrag angezeigt:

	![Informationen zu Koordinatoraufträgen](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

	Beachten Sie, dass nur erfolgreiche Ausführungen des Auftrags und nicht einzelne Aktionen innerhalb des geplanten Workflows angezeigt werden. Um diese anzuzeigen, wählen einen der Einträge vom Typ **Aktion** aus. Dadurch werden ähnliche abgerufene Informationen wie beim vorherigen Workflowauftrag angezeigt.

	![Aktionsinformationen](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##Problembehandlung

Bei der Behandlung von Problemen mit Oozie-Aufträgen ist die Oozie-Benutzeroberfläche sehr hilfreich, da sie Ihnen problemlos ermöglicht, sowohl Oozie-Protokolle als auch JobTracker-Protokolle für MapReduce-Aufgaben wie beispielsweise Hive-Abfragen anzuzeigen. Im Allgemeinen sollten Sie zur Problembehandlung wie folgt vorgehen:

1. Zeigen Sie den Auftrag auf der Oozie-Webbenutzeroberfläche an.

2. Wenn eine bestimmte Aktion nicht erfolgreich war, wählen Sie die Aktion aus, um festzustellen, ob das Feld **Error Message** weitere Informationen zum Fehler bietet.

3. Falls verfügbar, verwenden Sie die URL der Aktion, um weitere Details \(z. B. JobTracker-Protokolle\) für die Aktion anzuzeigen.

Es folgen Fehlermeldungen, die auftreten können, und Möglichkeiten zu ihrer Behebung.

###JA009: Cluster kann nicht initialisiert werden.

**Symptome**: Der Auftragsstatus ändert sich in **SUSPENDED**. In den Details für den Auftrag wird der Status von "RunHiveScript" als **START\_MANUAL** angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

	JA009: Cannot initialize Cluster. Please check your configuration for map

**Ursache**: Die in der Datei **job.xml** verwendeten WASB-Adressen enthalten nicht den Speichercontainer oder Namen des Speicherkontos. Das WASB-Adressformat muss `wasb://containername@storageaccountname.blob.core.windows.net` sein.

**Lösung**: Ändern Sie die vom Auftrag verwendeten WASB-Adressen.

###JA002: Oozie darf nicht die Identität von "&lt;USER\>" annehmen.

**Symptome**: Der Auftragsstatus ändert sich in **SUSPENDED**. In den Details für den Auftrag wird der Status von "RunHiveScript" als **START\_MANUAL** angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

	JA002: User: oozie is not allowed to impersonate <USER>

**Ursache**: Die aktuellen Berechtigungseinstellungen lassen nicht zu, dass Oozie die Identität des angegebenen Benutzerkontos annimmt.

**Lösung**: Oozie darf die Identität von Benutzern in der Gruppe **users** annehmen. Verwenden Sie `groups USERNAME`, um die Gruppen anzuzeigen, deren Mitglied das Benutzerkonto ist. Wenn der Benutzer nicht Mitglied der Gruppe **users** ist, verwenden Sie den folgenden Befehl, um den Benutzer der Gruppe hinzuzufügen:

	sudo adduser USERNAME users

> [AZURE.NOTE]Es dauert einige Minuten, bis HDInsight erkennt, dass der Benutzer der Gruppe hinzugefügt wurde.

###Launcher ERROR \(Sqoop\)

**Symptome**: Der Status wird in **KILLED** geändert. In den Details zum Auftrag wird der Status von "RunSqoopExport" als **ERROR** angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

	Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Ursache**: Sqoop kann den Datenbanktreiber nicht laden, der für den Zugriff auf die Datenbank erforderlich ist.

**Lösung**: Bei Verwendung von Sqoop in einem Oozie-Auftrag müssen Sie den Datenbanktreiber mit anderen vom Auftrag verwendeten Ressourcen \(z. B. workflow.xml\) angeben.

Sie müssen auch im Abschnitt `<sqoop>...</sqoop>` der Datei "workflow.xml" auf das Archiv mit dem Datenbanktreiber verweisen.

Für den Auftrag in diesem Dokument würden Sie z. B. folgendermaßen die folgenden Schritte ausführen:

1. Kopieren Sie die Datei "sqljdbc4.jar" in das Verzeichnis "/tutorials/useoozie":

		 hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /tutorials/useoozie/sqljdbc4.jar

2. Ändern Sie die Datei "workflow.xml" durch Hinzufügen des folgenden Codes in eine neue Zeile über `</sqoop>`:

		<archive>sqljdbc4.jar</archive>

##Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie ein Oozie-Workflow definiert und ein Oozie-Auftrag ausgeführt wird. Weitere Informationen zum Arbeiten mit HDInsight finden Sie in den folgenden Artikeln:

- [Verwenden des zeitbasierten Oozie-Koordinators mit HDInsight][hdinsight-oozie-coordinator-time]
- [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data]
- [Verwenden von Sqoop mit Hadoop in HDInsight][hdinsight-use-sqoop]
- [Verwenden von Hive mit Hadoop in HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit Hadoop in HDInsight][hdinsight-use-pig]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=July15_HO5-->