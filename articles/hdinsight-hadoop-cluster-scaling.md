<properties
   pageTitle="Clusterskalierung in HDInsight | Azure"
   description="Ändern Sie die Anzahl der Datenknoten in einem Cluster, der auf HDInsight ausgeführt wird, ohne den Cluster löschen und erneut erstellen zu müssen."
   services="hdinsight"
   documentationCenter=""
   authors="bradsev"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="bradsev"/>

#Clusterskalierung in HDInsight

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in HDInsight ausgeführten Cluster verwendeten Datenknoten ändern, ohne den Cluster löschen und neu erstellen zu müssen. Der Vorgang kann mithilfe von PowerShell, dem HDInsight SDK oder über das Azure-Portal ausgeführt werden.

> [WACOM.NOTE] In der aktuellen Version werden nur die Clustertypen Hadoop und Storm unterstützt. Die Unterstützung für HBase-Cluster wird in Kürze hinzugefügt. 

## Featuredetails
In diesem Abschnitt werden die Auswirkungen der Änderung der Anzahl der Datenknoten für die einzelnen Clustertypen beschrieben, die von HDInsight unterstützt werden:

* Hadoop
* Storm
* HBase 

## Hadoop 

### Hinzufügen von Datenknoten
Sie können die Anzahl der Datenknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

### Entfernen von Datenknoten
Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten verkleinert wird, werden einige der Dienste im Cluster neu gestartet. Dies führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.

## Storm
Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach dem erfolgreichen Abschluss der Skalierung müssen Sie die Topologie neu ausgleichen.

Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

* Storm-Webbenutzeroberfläche
* CLI-Tool 

Weitere Informationen finden Sie in der [Dokumentation zu Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Die Storm-Webbenutzeroberfläche ist im HDInsight-Cluster verfügbar:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Es folgt ein Beispiel, wie die Storm-Topologie mithilfe des CLI-Befehls neu ausgeglichen werden kann:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors and
	## the bolt "yellow-bolt" to use 10 executors.

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
Die Clusterskalierung wird für HBase-Cluster zu diesem Zeitpunkt nicht unterstützt.

## Voraussetzungen:

* Es werden ausschließlich Cluster mit der HDInsight-Version 3.1.3 oder höher unterstützt. Wenn Sie sich hinsichtlich der Version Ihres Clusters nicht sicher sind, können Sie die Clusterversion über das Azure-Portal überprüfen, indem Sie auf den Namen des HDInsight-Cluster klicken oder den Befehl  `Get-AzureHDInsightCluster -name <clustername>` von Azure PowerShell ausführen.

* Azure PowerShell, Version 0.8.14 oder höher, ist erforderlich, um den Vorgang über PowerShell auszuführen. Sie können die neueste Version von PowerShell auf der Website [Azure-Downloads](http://azure.microsoft.com/ downloads/) im Abschnitt "Befehlszeilentools" herunterladen. Sie können den folgenden Befehl in einem PowerShell-Fenster ausführen, um die installierte Azure PowerShell-Version zu prüfen: `(get-module Azure).Version`

## Verwenden der Clusterskalierung

### Azure-Portal
Die Größe eines aktiven Clusters kann über die Registerkarte **Skalieren** des Cluster-Dashboards von Azure HDInsight geändert werden.

![](http://i.imgur.com/u5Mewwx.png)

### PowerShell
Führen Sie den folgenden Befehl von einem Clientcomputer aus, um die Hadoop-Clustergröße mithilfe von PowerShell zu ändern:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [WACOM.NOTE] Auf dem Clientcomputer muss Azure PowerShell, Version 0.8.14 oder höher, installiert sein, damit Sie diesen Befehl verwenden können.

### SDK
Verwenden Sie eine der folgenden beiden Methoden, um die Hadoop-Clustergröße mithilfe des HDInsight SDKs zu ändern: 

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
	     		string location = "<ClusterLocation>"";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Weitere Informationen zur Verwendung des .NET SDKs finden Sie im Abschnitt [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/).
<!--HONumber=45--> 
