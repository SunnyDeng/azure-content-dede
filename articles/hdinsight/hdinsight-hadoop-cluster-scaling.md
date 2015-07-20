<properties
   pageTitle="Skalieren von Hadoop-, HBase- oder Apache Storm-Clustern in HDInsight | Microsoft Azure"
   description="Ändern Sie die Anzahl der Datenknoten in einem Hadoop-, HBase- oder Apache Storm-Cluster, der auf HDInsight ausgeführt wird, ohne den Cluster löschen und erneut erstellen zu müssen."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/30/2015"
   ms.author="jgao"/>

#Skalieren von Hadoop-, HBase- oder Apache Storm-Clustern zur Änderung deren Anzahl an Datenknoten in HDInsight 

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Datenknoten ändern, ohne den Cluster löschen und neu erstellen zu müssen. Der Vorgang kann über Azure PowerShell, das HDInsight SDK oder Azure-Portal erfolgen.

## Featuredetails
In diesem Abschnitt werden die Auswirkungen der Änderung der Anzahl der Datenknoten für die einzelnen Clustertypen beschrieben, die von HDInsight unterstützt werden:

* Hadoop
* Apache Storm
* HBase 

## Hadoop 

### Hinzufügen von Datenknoten
Sie können die Anzahl der Datenknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

### Entfernen von Datenknoten
Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Dies führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.

## Storm
Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichen Abschluss des Skalierungsvorgangs müssen Sie die Topologie neu ausgleichen.

Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

* Storm-Webbenutzeroberfläche
* Befehlszeilenschnittstelle (CLI) 

Weitere Informationen finden Sie in der Dokumentation zu [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Es folgt ein Beispiel, wie die Storm-Topologie mithilfe des CLI-Befehls neu ausgeglichen werden kann:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## Voraussetzungen

* Es werden nur Cluster mit HDInsight-Versionen ab 3.1.3 unterstützt. Wenn Sie sich hinsichtlich der Version Ihres Clusters nicht sicher sind, können Sie die Clusterversion im Azure-Portal überprüfen, indem Sie auf den Namen des HDInsight-Clusters klicken oder den Befehl `Get-AzureHDInsightCluster –name <clustername>` über Azure PowerShell ausführen.

* Eine Azure PowerShell-Version ab 0.8.14 ist erforderlich, um den Vorgang über Azure PowerShell auszuführen. Sie können die neueste Version von Azure PowerShell auf der Website [Azure-Downloads](http://azure.microsoft.com/downloads/) im Abschnitt **Befehlszeilentools** herunterladen. Sie können den folgenden Befehl in einem Azure PowerShell-Fenster ausführen, um die installierte Azure PowerShell-Version zu prüfen: `(get-module Azure).Version`

## Verwenden der Clusterskalierung

### Azure-Portal
Die Größe eines aktiven Clusters kann über die Registerkarte **SKALIEREN** des Clusterdashboards von Azure HDInsight geändert werden.

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
Führen Sie den folgenden Befehl auf einem Clientcomputer aus, um die Hadoop-Clustergröße mithilfe von Azure PowerShell zu ändern:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]Auf dem Clientcomputer muss Azure PowerShell, Version 0.8.14 oder höher, installiert sein, damit Sie diesen Befehl verwenden können.

### SDK
Verwenden Sie eine der folgenden Methoden, um die Hadoop-Clustergröße mithilfe des HDInsight SDK zu ändern:

	ChangeClusterSize(string dnsName, string location, int newSize) 

oder

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Sowohl die synchrone als auch die asynchrone Version dieser Methode gibt das Objekt [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx) zurück.

Hier folgt ein Beispielcode, der die Verwendung der synchronen Version dieser Methode veranschaulicht:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Weitere Informationen zur Verwendung des HDInsight .NET SDK finden Sie im Abschnitt [Benutzerdefinierte Bereitstellung von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md).
 

<!---HONumber=July15_HO2-->