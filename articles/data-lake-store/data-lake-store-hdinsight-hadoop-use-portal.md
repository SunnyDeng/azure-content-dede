<properties 
   pageTitle="Bereitstellen von HDInsight Hadoop-Clustern mit Azure Data Lake-Speicher mithilfe des Portals | Azure" 
   description="Verwenden des Azure-Portals zum Konfigurieren und Verwenden von HDInsight Hadoop-Clustern mit Azure Data Lake-Speicher" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Bereitstellen eines HDInsight-Clusters mit Data Lake-Speicher mithilfe des Azure-Portals

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Erfahren Sie, wie Sie das Azure-Portal zum Konfigurieren eines HDInsight-Clusters (Hadoop, HBase oder Storm) verwenden, damit dieser mit einem Azure Data Lake-Speicher genutzt werden kann. Einige wichtige Hinweise zu dieser Version:

* **Für Hadoop- und Storm-Cluster (Windows und Linux)** kann der Data Lake-Speicher nur als zusätzliches Speicherkonto verwendet werden. Standardspeicherkonten für solche Cluster sind weiterhin Azure-Speicherblobs (WASB).

* Sie können **für HBase-Cluster (Windows und Linux)** den Data Lake-Speicher als Standardspeicher oder als Zusatzspeicher verwenden.


In diesem Artikel stellen wir einen Hadoop-Cluster mit Data Lake-Speicher als zusätzlichem Speicher bereit. Zum Konfigurieren von HDInsight für den Data Lake-Speicher mithilfe des Azure-Portals sind die folgenden Schritte erforderlich:

* Erstellen eines HDInsight-Clusters mit Authentifizierung für einen Azure Active Directory-Dienstprinzipal
* Konfigurieren des Zugriffs auf den Data Lake-Speicher mit demselben Dienstprinzipal
* Ausführen eines Testauftrags im Cluster

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivieren Ihres Azure-Abonnements** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup).


## Erstellen eines HDInsight-Clusters mit Authentifizierung für einen Azure Active Directory-Dienstprinzipal

In diesem Abschnitt erstellen Sie einen HDInsight Hadoop-Cluster, für den der Data Lake-Speicher als zusätzlicher Speicher verwendet wird. In dieser Version kann der Data Lake-Speicher für einen Hadoop-Cluster nur als zusätzlicher Speicher für den Cluster verwendet werden. Als Standardspeicher werden weiterhin Azure Storage-BLOBS (WASB) verwendet. Wir erstellen daher zuerst das Speicherkonto und die Speichercontainer, die für den Cluster erforderlich sind.

1. Melden Sie sich beim neuen [Azure-Portal](https://portal.azure.com) an.

2. Führen Sie die Schritte unter [Erstellen von Hadoop-Clustern in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) aus, um mit der Bereitstellung eines HDInsight-Clusters zu beginnen.
 
3. Klicken Sie auf dem Blatt **Optionale Konfiguration** auf **Datenquelle**. Geben Sie auf dem Blatt **Datenquelle** die Details für das Speicherkonto und den Speichercontainer an, geben Sie den **Standort** als **USA (Osten) 2** an, und klicken Sie dann auf **Cluster-AAD-Identität**.

	![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Dienstprinzipal für HDInsight-Cluster hinzufügen")

4. Auf dem Blatt **Cluster-AAD-Identität** können Sie einen vorhandenen Dienstprinzipal auswählen oder einen neuen erstellen.
	
	* **Erstellen eines neuen Dienstprinzipals**: Klicken Sie auf dem Blatt **Cluster-AAD-Identität** auf **Neu erstellen**. Klicken Sie auf **Dienstprinzipal**, und geben Sie dann auf dem Blatt **Dienstprinzipal erstellen** Werte zum Erstellen eines neuen Dienstprinzipals an. Hierbei werden auch ein Zertifikat und eine Azure Active Directory-Anwendung erstellt. Klicken Sie auf **Erstellen**.

		![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Dienstprinzipal für HDInsight-Cluster hinzufügen")

		Klicken Sie auf dem Blatt **Cluster-AAD-Identität** auf **Auswählen**, um mit dem Dienstprinzipal fortzufahren, der erstellt werden soll.

		![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Dienstprinzipal für HDInsight-Cluster hinzufügen")


	* **Auswählen eines vorhandenen Dienstprinzipals**: Klicken Sie auf dem Blatt **Cluster-AAD-Identität** auf **Vorhandene verwenden**. Klicken Sie auf **Dienstprinzipal**, und suchen Sie auf dem Blatt **Dienstprinzipal auswählen** nach einem vorhandenen Dienstprinzipal. Klicken Sie auf den Namen eines Dienstprinzipals und dann auf **Auswählen**.

		![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Dienstprinzipal für HDInsight-Cluster hinzufügen")

		Laden Sie auf dem Blatt **Cluster-AAD-Identität** das zuvor erstellte Zertifikat (PFX) hoch, und geben Sie das Kennwort an, das Sie zum Erstellen des Zertifikats verwendet haben. Klicken Sie auf **Auswählen**. Die Azure Active Directory-Konfiguration für HDInsight-Cluster ist jetzt abgeschlossen.

		![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Dienstprinzipal für HDInsight-Cluster hinzufügen")

6. Klicken Sie auf dem Blatt **Datenquelle** auf **Auswählen**, und fahren Sie mit der Clusterbereitstellung wie unter [Erstellen von Hadoop-Clustern in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) beschrieben fort.

7. Nach der Bereitstellung des Clusters können Sie überprüfen, ob der Dienstprinzipal dem HDInsight-Cluster zugeordnet wurde. Klicken Sie hierzu auf dem Clusterblatt auf **Einstellungen** und **Cluster-AAD-Identität**. Der zugeordnete Dienstprinzipal sollte jetzt angezeigt werden.

	![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Dienstprinzipal für HDInsight-Cluster hinzufügen")

## <a name="acl"></a>Konfigurieren des Dienstprinzipals für den Zugriff auf das Dateisystem des Data Lake-Speichers

1. Melden Sie sich beim neuen [Azure-Portal](https://portal.azure.com) an.

2. Erstellen Sie ein Data Lake-Speicherkonto, falls Sie noch keins besitzen. Führen Sie die Schritte der Anleitung unter [Erste Schritte mit dem Azure Data Lake-Speicher mithilfe des Azure-Portals](data-lake-store-get-started-portal.md) aus.

	Falls Sie bereits über ein Data Lake-Speicherkonto verfügen, klicken Sie im linken Bereich auf **Durchsuchen**, auf **Data Lake-Speicher** und dann auf den Namen des Kontos, auf das Sie Zugriff gewähren möchten.

	Führen Sie unter Ihrem Data Lake-Speicherkonto die folgenden Aufgaben aus.

	* [Erstellen Sie einen Ordner im Data Lake-Speicher](data-lake-store-get-started-portal.md#createfolder).
	* [Laden Sie eine Datei in Ihren Data Lake-Speicher hoch](data-lake-store-get-started-portal.md#uploaddata). Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData) herunterladen.

	Sie verwenden die hochgeladenen Dateien später, wenn Sie das Data Lake-Speicherkonto mit dem HDInsight-Cluster testen.

3. Klicken Sie auf dem Blatt „Data Lake-Speicher“ auf **Daten-Explorer**.

	![Daten-Explorer](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "Daten-Explorer")

4. Klicken Sie auf dem Blatt **Daten-Explorer** auf den Stamm Ihres Kontos und dann auf dem Kontoblatt auf das Symbol **Zugriff**.

	![Zugriffssteuerungslisten für Data Lake-Dateisystem festlegen](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "Zugriffssteuerungslisten für Data Lake-Dateisystem festlegen")

5. Auf dem Blatt **Zugriff** sind der Standardzugriff und der benutzerdefinierte Zugriff aufgeführt, die dem Stamm bereits zugewiesen wurden. Klicken Sie auf das Symbol **Hinzufügen**, um Zugriffssteuerungslisten auf benutzerdefinierter Ebene hinzuzufügen und den zuvor erstellten Dienstprinzipal einzubeziehen.

	![Standardzugriff und benutzerdefinierten Zugriff auflisten](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "Standardzugriff und benutzerdefinierten Zugriff auflisten")

6. Klicken Sie auf das Symbol **Hinzufügen**, um das Blatt **Benutzerdefinierten Zugriff hinzufügen** zu öffnen. Klicken Sie auf diesem Blatt auf **Benutzer oder Gruppe auswählen**, und suchen Sie auf dem Blatt **Benutzer oder Gruppe auswählen** dann nach dem Dienstprinzipal, den Sie in Azure Active Directory bereits erstellt haben. Der Name des bereits erstellten Dienstprinzipals lautet **HDIADL**. Klicken Sie auf den Namen des Dienstprinzipals und dann auf **Auswählen**.

	![Gruppe hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "Gruppe hinzufügen")

7. Klicken Sie auf **Berechtigungen auswählen**, wählen Sie die Berechtigungen aus, die Sie dem Dienstprinzipal zuweisen möchten, und klicken Sie dann auf **OK**.

	![Berechtigungen für Gruppe zuweisen](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "Berechtigungen für Gruppe zuweisen")

8. Klicken Sie auf dem Blatt **Benutzerdefinierten Zugriff hinzufügen** auf **OK**. Die neu hinzugefügte Gruppe mit den zugeordneten Berechtigungen wird jetzt auf dem Blatt **Zugriff** aufgelistet.

	![Berechtigungen für Gruppe zuweisen](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "Berechtigungen für Gruppe zuweisen")

7. Falls erforderlich, können Sie die Zugriffsberechtigungen auch ändern, nachdem Sie den Dienstprinzipal hinzugefügt haben. Deaktivieren oder aktivieren Sie das Kontrollkästchen für jeden Berechtigungstyp (Lesen, Schreiben, Ausführen) in Abhängigkeit davon, ob Sie die Berechtigung entfernen oder zuweisen möchten. Klicken Sie auf **Speichern**, um die Änderungen zu speichern, oder auf **Verwerfen**, um die Änderungen rückgängig zu machen.



## Ausführen von Testaufträgen im HDInsight-Cluster zur Verwendung des Azure Data Lake-Speichers

Nachdem Sie einen HDInsight-Cluster konfiguriert haben, können Sie Testaufträge für den Cluster ausführen, um zu testen, ob der HDInsight-Cluster auf Daten im Azure Data Lake-Speicher zugreifen kann. Hierzu führen wir einige Hive-Abfragen für den Data Lake-Speicher aus.

1. Öffnen Sie das Clusterblatt für den Cluster, den Sie bereitgestellt haben, und klicken Sie dann auf **Dashboard**.

	![Clusterdashboard starten](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Clusterdashboard starten")

	Geben Sie die Administrator-Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

2. Die Microsoft Azure HDInsight-Abfragekonsole wird geöffnet. Klicken Sie auf **Hive-Editor**.

	![Hive-Editor öffnen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Hive-Editor öffnen")

3. Geben Sie im Hive-Editor die folgende Abfrage ein, und klicken Sie dann auf **Senden**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	In dieser Hive-Abfrage erstellen wir eine Tabelle aus den Daten, die im Data Lake-Speicher unter `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder` gespeichert sind. An diesem Speicherort befindet sich eine Beispieldatendatei, die Sie zuvor hochgeladen haben sollten.

	Unten in der Tabelle **Auftragssitzung** wird angezeigt, wie sich der Status des Auftrags von **Wird initialisiert** in **Wird ausgeführt** und **Abgeschlossen** ändert. Sie können auch auf **Details anzeigen** klicken, um weitere Informationen zum abgeschlossenen Auftrag anzuzeigen.

	![Tabelle erstellen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Tabelle erstellen")

4. Führen Sie die folgende Abfrage aus, um sicherzustellen, dass die Tabelle erstellt wurde.

		SHOW TABLES;

	Klicken Sie für diese Abfrage auf „Details anzeigen“. Die Ausgabe sollte wie folgt angezeigt werden:

		hivesampletable
		vehicles

	**vehicles** ist die Tabelle, die Sie bereits erstellt haben. **hivesampletable** ist eine Beispieltabelle, die in allen HDInsight-Clustern standardmäßig verfügbar ist.

5. Sie können auch eine Abfrage ausführen, um Daten aus **vehicles** abzurufen.

		SELECT * FROM vehicles LIMIT 5;

## Zugreifen auf den Data Lake-Speicher mit HDFS-Befehlen

Nachdem Sie den HDInsight-Cluster für die Verwendung des Data Lake-Speichers konfiguriert haben, können Sie die HDFS-Shellbefehle verwenden, um auf den Speicher zuzugreifen.

1. Melden Sie sich beim neuen [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Durchsuchen**, auf **HDInsight-Cluster** und dann auf den HDInsight-Cluster, den Sie erstellt haben.

3. Klicken Sie auf dem Clusterblatt auf **Remotedesktop** und dann auf dem Blatt **Remotedesktop** auf **Verbinden**.

	![Remoteverbindung mit HDI-Cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Erstellen einer Azure-Ressourcengruppe")

	Geben Sie bei Aufforderung die Anmeldeinformationen ein, die Sie für den Remotedesktopbenutzer bereitgestellt haben.

4. Starten Sie in der Remotesitzung die Windows PowerShell, und verwenden Sie die HDFS-Dateisystembefehle, um die Dateien im Azure Data Lake-Speicher aufzulisten.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Hierbei sollte auch die Datei aufgeführt werden, die Sie bereits in den Data Lake-Speicher hochgeladen haben.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	Sie können auch den Befehl `hdfs dfs -put` verwenden, um Dateien in den Data Lake-Speicher hochzuladen, und dann mit `hdfs dfs -ls` überprüfen, ob der Upload der Dateien erfolgreich war.

## Aspekte der Bereitstellung von HBase-Clustern, für die der Data Lake-Speicher als Standardspeicher verwendet wird

Für HBase-Cluster können Sie Data Lake-Speicherkonten als Standardspeicher verwenden. Falls Sie sich für diese Vorgehensweise entscheiden, muss Folgendes erfüllt sein, damit ein Cluster erfolgreich bereitgestellt werden kann: Der Dienstprinzipal, der dem Cluster zugeordnet ist, **muss** Zugriff auf das Data Lake-Speicherkonto haben. Dies können Sie auf zwei Arten sicherstellen:

* **Bei Verwendung eines vorhandenen Dienstprinzipals**: Sie müssen sicherstellen, dass der Dienstprinzipal der Zugriffssteuerungsliste auf der Stammebene des Dateisystems des Data Lake-Speichers hinzugefügt wird, bevor Sie mit der Bereitstellung des Clusters beginnen.
* **Bei Erstellung eines neuen Dienstprinzipals** im Rahmen der Clusterbereitstellung: Sobald die Clusterbereitstellung beginnt, müssen Sie den neu erstellten Dienstprinzipal der Stammebene des Dateisystems des Data Lake-Speichers hinzufügen. Wenn Sie dies nicht tun, wird der Cluster zwar bereitgestellt, aber beim Starten der HBase-Dienste tritt ein Fehler auf. Zur Umgehung dieses Problems müssen Sie den Dienstprinzipal der Zugriffssteuerungsliste des Data Lake-Speicherkontos hinzufügen und die HBase-Dienste über die Ambari-Webbenutzeroberfläche dann neu starten.

Eine Anleitung, wie Sie dem Dateisystem eines Data Lake-Speichers einen Dienstprinzipal hinzufügen, finden Sie unter [Konfigurieren des Dienstprinzipals für den Zugriff auf das Dateisystem des Data Lake-Speichers](#acl).



## Weitere Informationen

* [PowerShell: Erstellen eines HDInsight-Clusters für die Verwendung des Data Lake-Speichers](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_1210_2015-->