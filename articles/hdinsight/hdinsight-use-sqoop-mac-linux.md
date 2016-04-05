<properties
	pageTitle="Verwenden von Hadoop Sqoop in Linux-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie einen Sqoop-Import und -Export zwischen einem Linux-basierten HDInsight-Cluster und einer Azure-SQL-Datenbank durchführen können."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#Verwenden von Sqoop mit Hadoop in HDInsight (SSH)

[AZURE.INCLUDE [Sqoop-Auswahl](../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sqoop zum Importieren und Exportieren zwischen einem Linux-basierten HDInsight-Cluster und einer Azure SQL-Datenbank oder einer SQL-Server-Datenbank verwendet werden kann.

> [AZURE.NOTE] In den Schritten in diesem Artikel wird mit SSH eine Verbindung zu einem Linux-basierten HDInsight-Cluster hergestellt. Windows-Clients können auch mit Azure PowerShell verwenden, um auf Linux-basierten Clustern mit Sqoop zu arbeiten, gemäß der Beschreibung in [Verwenden von Sqoop mit Hadoop in HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##Was ist Sqoop?

Während Hadoop die beste Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z. B. Protokolle und Dateien ist, besteht oft auch Bedarf für die Verarbeitung strukturierter Daten, die in relationalen Datenbanken gespeichert werden.

[Sqoop][sqoop-user-guide-1.4.4] ist ein Tool zum Übertragen von Daten zwischen Hadoop-Clustern und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und sie anschließend wieder in ein RDBMS exportieren. In diesem Beispiel verwenden Sie eine SQL-Serverdatenbank als relationale Datenbank.

Informationen zu den unterstützten Sqoop-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions].


##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Arbeitsstation**: ein Computer mit einem SSH-Client.

- **Azure CLI**: Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../xplat-cli-install.md)

##Das Szenario

Ein HDInsight-Cluster wird mit einigen Beispieldaten geliefert. Sie verwenden eine Hive-Tabelle namens **hivesampletable**, die auf die Datendatei unter **wasb:///hive/warehouse/hivesampletable** verweist. Die Tabelle enthält einige Mobilgerätedaten. Die Hive-Tabelle hat folgendes Schema:

| Feld | Datentyp |
| ----- | --------- |
| clientid | string |
| querytime | string |
| market | string |
| deviceplatform | string |
| devicemake | string |
| devicemodel | string |
| state | string |
| country | string |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

Zunächst exportieren Sie **hivesampletable** in die Azure SQL-Datenbank oder in SQL-Server in eine Tabelle mit dem Namen **mobiledata** und importieren diese Tabelle dann wieder in HDInsight unter **wasb:///tutorials/usesqoop/importeddata**.


## Erstellen von Cluster und SQL-Datenbank

1. Klicken Sie auf die folgende Abbildung, um eine ARM-Vorlage im Azure-Portal zu öffnen.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Die ARM-Vorlage befindet sich in einem öffentlichen Blobcontainer, **https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*.
    
    Die ARM-Vorlage ruft ein bacpac-Paket auf, um die Tabellenschemas der SQL-Datenbank bereitzustellen. Das bacpac-Paket befindet sich ebenfalls in einem öffentlichen Blobcontainer, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Wenn Sie einen privaten Container für die bacpac-Dateien verwenden möchten, verwenden Sie die folgenden Werte in der Vorlage:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. Geben Sie auf dem Blatt "Parameter" Folgendes ein:

    - **ClusterName**: Geben Sie einen Namen für den Hadoop-Cluster ein, den Sie erstellen möchten.
    - **Cluster-Anmeldename und -Kennwort**: Der Standardname für die Anmeldung lautet „admin“.
    - **SSH-Benutzername und -Kennwort**.
    - **Anmeldename und Kennwort für den SQL-Datenbankserver**.

    Die folgenden Werte sind im Abschnitt mit den Variablen hartcodiert:
    
    |Name des Standard-Speicherkontos|<CluterName>store|
    |----------------------------|-----------------|
    |Servername der Azure SQL-Datenbank|<ClusterName>dbserver|
    |Azure SQL-Datenbankname|<ClusterName>db|
    
    Bitte notieren Sie diese Werte. Sie werden diese später im Lernprogramm benötigen.
    
3\. Klicken Sie auf **OK**, um die Parameter zu speichern.

4\. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf das Dropdownfeld **Ressourcengruppe** und dann auf **Neu**, um eine neue Ressourcengruppe zu erstellen. Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere verknüpfte Ressourcen gruppiert werden.

5\. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.

6\. Klicken Sie auf **Erstellen**. Daraufhin wird eine neue Kachel mit der Bezeichnung "Bereitstellung für Vorlagenbereitstellung wird gesendet" angezeigt. Das Erstellen des Clusters und der SQL-Datenbank dauert ca. 20 Minuten.

Wenn Sie die vorhandene Azure SQL-Datenbank oder Microsoft SQL Server verwenden möchten:

- **Azure SQL-Datenbank**: Sie müssen eine Firewall-Regel für den Azure SQL-Datenbankserver konfigurieren, um Zugriff auf Ihre Arbeitsstation zu erlauben. Anweisungen zur Erstellung einer Azure SQL-Datenbank und zur Konfiguration der Firewall erhalten Sie unter [Erste Schritte mit Azure SQL-Datenbanken][sqldatabase-get-started]. 

    > [AZURE.NOTE] Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Portal aktivieren. Anweisungen zum Erstellen einer Azure SQL-Datenbank und zum Konfigurieren von Firewall-Regeln finden Sie unter [Erstellen und Konfigurieren einer SQL-Datenbank][sqldatabase-create-configue].

- **SQL Server**: Falls sich Ihr HDInsight-Cluster im gleichen virtuellen Netzwerk in Azure wie ein SQL Server befindet, können Sie mit den hier beschriebenen Schritten Daten in einer SQL Server-Datenbank importieren und exportieren.

    > [AZURE.NOTE] HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.

    * Weitere Informationen zur Erstellung und Konfiguration von virtuellen Netzwerken finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](../services/virtual-machines/).

        * Wenn Sie SQL Server in Ihrem Rechenzentrum verwenden, müssen Sie das virtuelle Netzwerk entweder als *Standort-zu-Standort* oder als *Punkt-zu-Standort* konfigurieren.

            > [AZURE.NOTE] Für virtuelle Netzwerke im **Punkt-zu-Standort-Modus** muss die Anwendung zum Konfigurieren des VPN-Clients auf dem SQL Server ausgeführt werden. Diese Anwendung ist im **Dashboard** der Konfiguration Ihres virtuellen Azure-Netzwerks verfügbar.

        * Wenn Sie SQL Server auf einem virtuellen Azure-Computer verwenden, können Sie eine beliebige Konfiguration für das virtuelle Netzwerk nehmen, sofern sich der virtuelle Computer, auf dem SQL Server läuft, im gleichen virtuellen Netzwerk befindet wie HDInsight.

    * Hinweise zum Erstellen eines HDInsight-Clusters in einem virtuellen Netzwerk finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight mit benutzerdefinierten Optionen](hdinsight-provision-clusters.md).

    > [AZURE.NOTE] Der SQL Server muss eine Authentifizierung zulassen. Sie benötigen eine SQL-Serveranmeldung, um die Schritte in diesem Artikel abzuschließen.
	

##Sqoop-Export

2. Verwenden Sie den folgenden Befehl, um einen Link zum SQL Server JDBC-Treiber aus dem Sqoop-Verzeichnis "lib" zu erstellen. Dies ermöglicht Sqoop die Nutzung dieses Treibers zur Kommunikation mit der SQL-Datenbank.

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob Sqoop Ihre SQL-Datenbank erreichen kann:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Es sollte eine Liste mit Datenbanken zurückgeliefert werden, in der auch die zuvor erstellte Datenbank **sqooptest** enthalten ist.

4. Verwenden Sie den folgenden Befehl, um Daten von **hivesampletable** in die Tabelle **mobiledata** zu exportieren:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Dies weist Sqoop an, sich mit SQL-Datenbank bzw. der Datenbank **sqooptest** zu verbinden und Daten aus **wasb:///hive/warehouse/hivesampletable** (physische Dateien der *hivesampletable*) in die Tabelle **mobiledata** zu exportieren.

5. Nach Abschluss des Befehls stellen Sie wie folgt über TSQL eine Verbindung mit der Datenbank her:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Nachdem die Verbindung hergestellt wurde, überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle **mobiledata** exportiert wurden:

        SELECT * FROM mobiledata
        GO

    Es sollte eine Liste der Tabellendaten angezeigt werden. Geben Sie `exit` ein, um das Dienstprogramm tsql zu beenden.

##Sqoop-Import

1. Importieren Sie wie folgt Daten aus der Tabelle **mobiledata** in der SQL-Datenbank in das Verzeichnis **wasb:///tutorials/usesqoop/importeddata** auf HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Die importierten Daten enthalten Felder, die durch ein Tabstoppzeichen getrennt sind, und die Zeilen werden durch ein Zeilenumbruchzeichen abgeschlossen.

2. Sobald der Import abgeschlossen ist, verwenden Sie den folgenden Befehl zum Auflisten der Daten in dem neuen Verzeichnis:

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Verwenden von SQL Server

Sie können auch Sqoop zum Importieren und Exportieren von Daten aus SQL-Server verwenden, entweder in Ihrem Rechenzentrum oder auf einem in Azure gehosteten virtuellen Computer. Die Unterschiede zwischen der Verwendung von SQL-Datenbank und SQL Server sind:

* Sowohl HDInsight als auch SQL Server müssen sich im selben virtuellen Azure-Netzwerk befinden.

    > [AZURE.NOTE] HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.

    Wenn Sie SQL Server in Ihrem Rechenzentrum verwenden, müssen Sie das virtuelle Netzwerk entweder als *Standort-zu-Standort* oder als *Punkt-zu-Standort* konfigurieren.

    > [AZURE.NOTE] Für virtuelle Netzwerke im **Punkt-zu-Standort-Modus** muss die Anwendung zum Konfigurieren des VPN-Clients auf dem SQL Server ausgeführt werden. Diese Anwendung ist im **Dashboard** der Konfiguration Ihres virtuellen Azure-Netzwerks verfügbar.

    Informationen zur Erstellung und Konfiguration von virtuellen Netzwerken finden Sie unter [Aufgaben bei der Konfiguration virtueller Netzwerke](../services/virtual-machines/).

* SQL Server muss so konfiguriert sein, dass SQL-Authentifizierung erlaubt ist. Weitere Informationen finden Sie unter [Auswählen eines Authentifizierungsmodus](https://msdn.microsoft.com/ms144284.aspx).

* Sie müssen SQL Server möglicherweise für Remoteverbindungen konfigurieren. Weitere Informationen finden Sie unter [So beheben Sie Fehler bei der Verbindung zum SQL Server-Datenbankmodul](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Die Datenbank **sqooptest** wird in SQL Server mit einem Dienstprogramm wie z. B. **SQL Server Management Studio** oder **tsql** erstellt – die Schritte zur Verwendung der Azure-Befehlszeilenschnittstelle gelten nur für Azure SQL-Datenbank.

    Die tsql-Anweisungen zum Erstellen der Tabelle **mobiledata** ähneln denen für SQL-Datenbank, mit Ausnahme der Erstellung eines gruppierten Index – Dies ist für SQL Server nicht erforderlich:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Bei der Verbindung mit dem SQL-Server aus HDInsight müssen Sie möglicherweise die IP-Adresse des SQL-Servers verwenden, sofern kein DNS-System zur Namensauflösung in dem virtuellen Azure-Netzwerk konfiguriert wurde. Zum Beispiel:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Nächste Schritte

Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

- [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]\: Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
- [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-data]\: Verwenden von Hive zur Analyse von Daten zu Flugverspätungen und Verwenden von Sqoop zum Exportieren von Daten in die SQL-Datenbank.
- [Hochladen von Daten in HDInsight][hdinsight-upload-data]\: Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob-Speicher.



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!----HONumber=AcomDC_0323_2016-->