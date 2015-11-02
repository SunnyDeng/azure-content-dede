<properties
   	pageTitle="Bereitstellen von Hadoop-, HBase- oder Storm-Clustern unter Linux in HDInsight mithilfe der plattformübergreifenden Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Linux-basierte HDInsight-Cluster mit der plattformübergreifenden Azure-Befehlszeilenschnittstelle, Azure-Ressourcen-Manager-Vorlagen und der Azure-REST-API erstellen. Sie können den Cluster-Typ (Hadoop, HBase oder Storm) angeben oder Skripts verwenden, um benutzerdefinierte Komponenten zu installieren."
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
   	ms.date="10/14/2015"
   	ms.author="larryfr"/>

#Erstellen von Linux-basierten Clustern in HDInsight mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-create-linux-cluster-selector.md)]

Die Azure-Befehlszeilenschnittstelle ist ein plattformübergreifendes Befehlszeilentool zur Verwaltung von Azure-Diensten. Es kann mit Azure-Ressourcen-Manager-Vorlagen zum Erstellen eines HDInsight-Clusters verwendet werden, zusammen mit zugeordneten Speicherkonten und anderen Diensten.

Bei Azure-Ressourcen-Manager-Vorlagen handelt es sich um JSON-Dokumente, mit denen eine __Ressourcengruppe__ und alle darin enthaltenen Ressourcen (z. B. HDInsight) beschrieben werden. Dieser vorlagenbasierte Ansatz ermöglicht Ihnen das Definieren aller Ressourcen, die Sie für HDInsight innerhalb einer Vorlage benötigen, und das Verwalten von Änderungen an der gesamten Gruppe durch __Bereitstellungen__, mit denen Änderungen an der Gruppe vorgenommen werden.

Anhand der Schritte in diesem Dokument werden Sie durch die Erstellung eines neuen HDInsight-Clusters mithilfe der Azure-Befehlszeilenschnittstelle und einer Vorlage geführt:

##Voraussetzungen

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure-Befehlszeilenschnittstelle__. Informationen zum Installieren der Befehlszeilenschnittstelle finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

##Anmelden bei Ihrem Azure-Abonnement

Führen Sie die Schritte aus, die unter [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure Befehlszeilenschnittstelle (Azure CLI)](xplat-cli-connect.md) dokumentiert sind, und stellen Sie eine Verbindung mit Ihrem Abonnement über die __login__-Methode her.

##Erstellen eines Clusters

Die folgenden Schritte müssen in einer Befehlszeilen-, Shell- oder Terminalsitzung nach der Installation und Konfiguration der Azure-Befehlszeilenschnittstelle erfolgen.

1. Führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement zu authentifizieren:

        azure login

    Sie werden aufgefordert, Ihren Namen und das Kennwort anzugeben. Wenn Sie über mehrere Azure-Abonnements verfügen, können Sie `azure account set <subscriptionname>` zum Festlegen des Abonnements verwenden, das von den Befehlen der Azure-Befehlszeilenschnittstelle genutzt werden soll.

3. Wechseln Sie mit dem folgenden Befehl in den Azure-Ressourcen-Manager-Modus:

        azure config mode arm

4. Erstellen Sie eine Vorlage für Ihren HDInsight-Cluster. Unten sind einige einfache Beispielvorlagen angegeben:

    * [Linux-basierter Cluster mit öffentlichem SSH-Schlüssel](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-publickey)
    * [Linux-basierter Cluster mit Kennwort für das SSH-Konto](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)

    Mit beiden Vorlagen wird auch das von HDInsight verwendete Azure-Speicherkonto erstellt.

    Sie benötigen die Dateien __azuredeploy.json__ und __azuredeploy.parameters.json__. Kopieren Sie diese Dateien lokal, bevor Sie fortfahren.

5. Öffnen Sie die Datei __azuredeploy.parameters.json__ in einem Editor, und geben Sie Werte für die Elemente im Abschnitt `parameters` an:

    * __location__: Das Datencenter, in dem die Ressourcen erstellt werden. Sie können den Abschnitt `location` in der Datei __azuredeploy.json__ anzeigen, um eine Liste der zulässigen Standorte zu erhalten.
    * __clusterName__: Der Name des HDInsight-Clusters. Dieser Name muss eindeutig sein, da für die Bereitstellung sonst ein Fehler auftritt.
    * __clusterStorageAccountName__: Der Name des Azure-Speicherkontos, das für den HDInsight-Cluster erstellt wird. Dieser Name muss eindeutig sein, da für die Bereitstellung sonst ein Fehler auftritt.
    * __clusterLoginPassword__: Das Kennwort des Clusteradministratorbenutzers. Dies sollte ein sicheres Kennwort sein, weil damit auf Websites und REST-Dienste im Cluster zugegriffen wird.
    * __sshUserName__: Der Name des ersten SSH-Benutzers, der für diesen Cluster erstellt wird. SSH wird verwendet, um mit diesem Konto per Remoteverbindung auf diesen Cluster zuzugreifen. Der Name muss eindeutig sein, und es darf sich nicht um den Namen eines Kontos handeln, das im Cluster bereits verwendet wird. Beispiele für ungültige Namen sind „root“, „storm“ und „hbase“.
    * __sshPublicKey__: Wenn Sie die Vorlage nutzen, die einen öffentlichen SSH-Schlüssel erfordert, müssen Sie den öffentlichen Schlüssel in dieser Zeile hinzufügen. Weitere Informationen zum Generieren von öffentlichen Schlüsseln und zu ihrer Verwendung finden Sie in den folgenden Artikeln:

        * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: Wenn Sie die Vorlage verwenden, die ein SSH-Kennwort erfordert, müssen Sie in dieser Zeile ein Kennwort hinzufügen.

    Speichern und schließen Sie die Datei, wenn Sie dies erledigt haben.

5. Gehen Sie wie folgt vor, um eine leere Ressourcengruppe zu erstellen. Ersetzen Sie __RESOURCEGROUPNAME__ durch den Namen, den Sie für diese Gruppe verwenden möchten. Ersetzen Sie __LOCATION__ durch das Datencenter, in dem Sie Ihren HDInsight-Cluster erstellen möchten:

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]Wenn der Name des Standorts Leerzeichen enthält, setzen Sie ihn in doppelte Anführungszeichen. Beispiel: „USA, Mitte/Süden“.

6. Verwenden Sie den folgenden Befehl, um die erste Bereitstellung für diese Ressourcengruppe zu erstellen. Ersetzen Sie __PATHTOTEMPLATE__ durch den Pfad zur Vorlagendatei __azuredeploy.json__. Ersetzen Sie __PATHTOPARAMETERSFILE__ durch den Pfad zur Datei __azuredeploy.parameters.json__. Ersetzen Sie __RESOURCEGROUPNAME__ durch den Namen der Gruppe, die Sie im vorherigen Schritt erstellt haben:

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Nachdem die Bereitstellung akzeptiert wurde, sollte eine Meldung wie die folgende angezeigt werden: `group deployment create command ok`.

7. Es kann einige Zeit dauern, bis die Bereitstellung abgeschlossen ist (ca. 15 Minuten). Sie können Informationen zur Bereitstellung mit dem folgenden Befehl anzeigen. Ersetzen Sie __RESOURCEGROUPNAME__ durch den Namen der Ressourcengruppe aus dem vorherigen Schritt:

        azure group log show -l RESOURCEGROUPNAME
    
    Nach Abschluss die Bereitstellung enthält das Feld __Status__ den Wert __Succeeded__. Wenn während der Bereitstellung ein Fehler auftritt, erhalten Sie mit dem folgenden Befehl weitere Informationen zum Fehler:

        azure group log show -l -v RESOURCEGROUPNAME

##Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich erstellt haben, nutzen Sie die folgenden Informationen, um zu erfahren, wie Sie mit Ihrem Cluster arbeiten:

###Hadoop-Cluster

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

###HBase-Cluster

* [Erste Schritte mit HBase in HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Entwickeln von Java-Anwendung für HBase in HDInsight](hdinsight-hbase-build-java-maven-linux)

###Storm-Cluster

* [Entwickeln von Java-Topologien für Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten im Storm in HDInsight](hdinsight-storm-develop-python.md)
* [Bereitstellen und Überwachen von Topologien mit Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO4-->