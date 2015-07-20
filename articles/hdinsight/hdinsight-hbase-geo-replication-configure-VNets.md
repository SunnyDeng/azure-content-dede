<properties 
   pageTitle="Konfigurieren einer VPN-Verbindung zwischen zwei virtuellen Netzwerken | Microsoft Azure" 
   description="Erfahren Sie, wie Sie VPN-Verbindungen und die Domänennamenauflösung zwischen zwei virtuellen Azure-Netzwerken sowie die HBase-Georeplikation konfigurieren." 
   services="hdinsight,virtual-network" 
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
   ms.date="04/02/2015"
   ms.author="jgao"/>

# Konfigurieren einer VPN-Verbindung zwischen zwei virtuellen Netzwerken in Azure  

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 

Die virtuelle Azure-Netzwerk Standort-zu-Standort-Konnektivität verwendet ein VPN-Gateway, um einen sicheren Tunnel mit IPSec/IKE bereitzustellen. Die VNets können sich in verschiedenen Abonnements und Regionen befinden. Sie können sogar VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Es gibt mehrere Gründe für VNet zu-VNet-Konnektivität:

- Regionsübergreifende Georedundanz und Geopräsenz 
- Regionale Anwendungen mit mehreren Ebenen mit starker Isolierungsgrenze 
- Abonnementübergreifende Kommunikation zwischen Organisationen in Azure

Weitere Informationen finden Sie unter [Konfiguration einer VNet-zu-VNet-Verbindung](https://msdn.microsoft.com/library/azure/dn690122.aspx).

Video anzeigen:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Dieses Lernprogramm ist Teil der [Reihe][hdinsight-hbase-replication] zum Erstellen von HBase Georeplikation.

- Konfigurieren einer VPN-Konnektivität zwischen zwei virtuellen Netzwerken (dieses Lernprogramm)
- [Konfigurieren von DNS für virtuelle Netzwerke][hdinsight-hbase-geo-replication-DNS]
- [Konfigurieren von HBase-Georeplikation][hdinsight-hbase-geo-replication]

Das folgende Diagramm veranschaulicht die beiden virtuellen Netzwerke, die Sie in diesem Lernprogramm erstellen:

![HDInsight HBase Replikation virtuelles Netzwerkdiagramm][img-vnet-diagram]
 

##Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

		Add-AzureAccount

	Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

		Select-AzureSubscription <AzureSubscriptionName>


>[AZURE.NOTE]Azure Dienstnamen und die Namen der virtuellen Computer müssen eindeutig sein. Der in diesem Lernprogramm verwendete Name ist Contoso-[Azure Service/VM name]-[EU/US]. Contoso-VNet-EU ist z. B. das virtuelle Azure-Netzwerk im Rechenzentrum Nordeuropa; Contoso-DNS-US ist der DNS-Server VM im Datencenter im Osten der USA. Sie müssen sich Ihre eigenen Namen ausdenken.
 

##Erstellen Sie zwei Azure-VNets



**Zum Erstellen eines virtuellen Netzwerks namens Contoso-VNet-EU in Nordeuropa**

1.	Melden Sie sich auf dem [Azure-Portal][azure-portal] an.
2.	Klicken Sie auf **NEU**, **NETWORK SERVICES**, **VIRTUELLES NETZWERK**, ** BENUTZERDEFINIERT ERSTELLEN**.
3.	Geben Sie Folgendes ein:

	- **NAME**: Contoso-VNet-EU
	- **STANDORT**: Nordeuropa

		In diesem Lernprogramm werden Datencenter in Nordeuropa und im Osten der USA verwendet. Sie können Ihr eigenes Datencenter auswählen.
4.	Geben Sie Folgendes ein:

	- **DNS-SERVER**: (Lassen Sie diese Angabe leer) 
	
		Für die Namensauflösung innerhalb virtueller Netzwerke benötigen Sie einen eigenen DNS-Server. Wann die Verwendung der von Azure bereitgestellten Namensauflösung bzw. die Verwendung eines eigenen DNS-Servers angebracht ist, erfahren Sie unter [Namensauflösung (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx). Anweisungen zur Konfiguration der Namensauflösung zwischen VNets finden Sie unter [Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-dns].
  
	- **Punkt-zu-Standort-VPN konfigurieren**: (nicht aktiviert)

		Punkt-zu-Standort ist auf dieses Szenario nicht anwendbar.

 	- **Standort-zu-Standort-VPN konfigurieren**: (nicht aktiviert)
 	
		Die Standort-zu-Standort-VPN-Verbindung mit dem virtuellen Azure-Netzwerk konfigurieren Sie für das Datencenter im Osten der USA.
5.	Geben Sie Folgendes ein:

	- 	**ADRESSRAUM START-IP**: 10.1.0.0
	- 	**ADRESSRAUM CIDR**: / 16
	- 	**Subnet-1-START-IP-**: 10.1.0.0
	- 	**Subnet-1 CIDR**: / 24

	Dieser Adressraum darf nicht mit dem virtuellen US-Netzwerk überlappen.

**Zum Erstellen eines virtuellen Netzwerks namens Contoso-VNet-EU in Westeuropa**

- Wiederholen Sie das letzte Verfahren mit den folgenden Werten:

	- **NAME**: Contoso-VNet-US
	- **STANDORT**: USA, Osten
	 
	- **DNS-SERVER**: (Lassen Sie diese Angabe leer)
	- **Punkt-zu-Standort-VPN konfigurieren**: (nicht aktiviert)
	- **Standort-zu-Standort-VPN konfigurieren**: (nicht aktiviert)
	 
	- **ADRESSRAUM START-IP**: 10.2.0.0
	- **ADRESSRAUM CIDR**: / 16
	- **Subnet-1-START-IP-**: 10.2.0.0
	- **Subnet-1 CIDR**: / 24

















##Konfigurieren Sie eine VPN-Verbindung zwischen den beiden VNets

###Erstellen lokaler Netzwerke

Wenn Sie eine VNet-zu-VNet-Konfiguration erstellen, müssen Sie jedes VNet so konfigurieren, dass die gegenseitige Identifizierung als lokaler Netzwerkstandort erfolgt. In diesem Abschnitt konfigurieren Sie jedes VNet als ein lokales Netzwerk. Die lokalen Netzwerke teilen sich die gleichen IP-Adressbereiche im entsprechenden VNet.

![Konfigurieren von Azure-VPN-Standort-zu-Standort-Konfiguration - Azure lokale Netzwerke][img-vnet-lnet-diagram]


**Erstellen Sie ein lokales Netzwerk mit dem Namen Contoso-LNet-EU das mit dem Contoso-VNet-EU-Netzwerk-Adressbereich übereinstimmt**

1. Klicken Sie im Azure-Portal auf **NEU**, **NETWORK SERVICES**, **VIRTUELLES NETZWERK**, **LOKALES NETZWERK HINZUFÜGEN**.
3. Geben Sie Folgendes ein:

	- **NAME**: Contoso-LNet-EU
	- **IP-ADRESSE DES VPN-GERÄTS**: 192.168.0.1 (diese Adresse wird später aktualisiert)

		Normalerweise verwenden Sie die tatsächliche externe IP-Adresse für ein VPN-Gerät. Für VNet-zu-VNet-Konfigurationen verwenden Sie die IP-Adresse des VPN-Gateways. Angesichts der Tatsache, dass Sie die VPN-Gateways für die beiden VNets noch nicht erstellt haben, geben Sie zunächst eine beliebige IP-Adresse ein, die Sie später korrigieren.
4.	Geben Sie Folgendes ein:

	- **ADRESSRAUM START-IP:** 10.1.0.0
	- **ADRESSRAUM CIDR:** / 16
	
	Diese Angaben müssen genau dem Bereich entsprechen, den Sie in einem früheren Schritt für Contoso-VNet-EU angegeben haben.

**Erstellen Sie ein lokales Netzwerk mit dem Namen Contoso-LNet-US das mit dem Contoso-VNet-US-Netzwerk-Adressbereich übereinstimmt**

- Wiederholen Sie das letzte Verfahren mit den folgenden Parametern:

	- **NAME**: Contoso-LNet-US
	- **IP-ADRESSE DES VPN-GERÄTS**: 192.168.0.1 (diese Adresse wird später aktualisiert)
	 
	- **ADRESSRAUM START-IP**: 10.2.0.0
	- **ADRESSRAUM CIDR**: / 16


###Erstellen von VPN-Gateways

Diese Konfiguration umfasst zwei Teile. Zunächst konfigurieren Sie eine VNet-Standort-zu-Standort-Verbindung zu einem lokalen Netzwerk und erstellen dann ein VPN für dynamisches Routing. VNet-zu-VNet erfordert Azure-VPN-Gateways mit VPNs mit dynamischem Routing. Azure VPNs mit statischem Routing werden nicht unterstützt.

**Zum Konfigurieren der Contoso-VNet-EU Standort-zu-Standort-Verbindung auf Contoso-LNet-US**

1.	Klicken Sie im Azure-Portal auf **NETZWERKE** auf der linken Seite,
2.	Klicken Sie auf **Contoso-VNet-EU**
3.	Klicken Sie auf die Registerkarte **KONFIGURIEREN**.
4.	Aktivieren Sie **Herstellen einer Verbindung mit dem lokalen Netzwerk**.
5.	Klicken Sie unter **LOKALES NETZWERK** auf **Contoso-LNet-US**.
6.	Klicken Sie auf **Gateway-Subnetz hinzufügen** im Abschnitt Adressräume des virtuellen Netzwerks.
7.	Klicken Sie auf **SPEICHERN**.
8.	Klicken Sie auf **OK**, um zu bestätigen.


**Erstellen eines VPN-Gateway für Contoso-VNet-EU**

1.	Klicken Sie im Azure-Portal auf die Registerkarte **DASHBOARD**.
4.	Klicken Sie auf unten auf der Seite auf **GATEWAY ERSTELLEN** und anschließend auf **Dynamisches Routing**.
5.	Klicken Sie auf **Ja**, um zu bestätigen. Die Gatewaygrafik auf der Seite wechselt zu Gelb und zeigt „Gateway wird erstellt“ an. Die Erstellung des Gateways nimmt normalerweise 15 Minuten in Anspruch.

	Wenn sich der Gatewaystatus in „Connecting“ ändert, wird die IP-Adresse für jedes Gateway im Dashboard angezeigt. Notieren Sie sich die IP-Adressen, die für jedes VNet gelten. Gehen Sie dabei sorgfältig vor, um sie nicht zu verwechseln. Dies sind die IP-Adressen, die verwendet werden, wenn Sie die IP-Platzhalteradressen für das VPN-Gerät unter „Lokale Netzwerke“ bearbeiten.

6.	Erstellen Sie eine Kopie der **GATEWAY IP-ADDRESSE**. Sie werden diese verwenden, um im nächsten Abschnitt den VPN-Gateway für Contoso-VNet-EU zu konfigurieren.

**Erstellen eines VPN-Gateway für Contoso-VNet-EU**

- Wiederholen Sie die letzten zwei Verfahren zum Konfigurieren der Contoso-VNet-US Standort-zu-Standort-Verbindung mit Contoso-LNet-EU und erstellen Sie dann ein VPN-Gateway für Contoso-Vnet-US. Wenn Sie fertig sind, verfügen Sie über die VPN-Gateway IP-Adresse für Contoso-VNet-US.


### Stellen Sie die VPN-Gerät IP-Adressen für lokale Netzwerke ein
Im letzten Abschnitt können Sie ein VPN-Gateway für jedes der VNets erstellen. Sie haben die IP-Adressen der VPN-Gateways. Sie können jetzt zurück zur Konfiguration der IP-Adressen im lokalen Netzwerk VPN-Gerät gehen.

**So konfigurieren Sie die IP-Adresse des VPN-Geräts für Contoso-LNet-EU**

1.	Klicken Sie im Azure-Portal auf **NETZWERKE** auf der linken Seite.
2.	Klicken Sie oben auf **LOKALE NETZWERKE**.
3.	Klicken Sie auf **Contoso-LNet-EU-** und klicken Sie dann unten auf **BEARBEITEN**.
4.	Aktualisieren der **IP-ADRESSE DES VPN-GERÄTS**. Das ist die Adresse, die Sie von der Registerkarte DASHBOARD von Contoso-VNET-EU erhalten.
5.	Klicken Sie auf die rechte Taste.
6.	Klicken Sie dann die Taste „Aktivieren“.

**So konfigurieren Sie die IP-Adresse des VPN-Geräts für Contoso-LNet-US**

- Wiederholen Sie das letzte Verfahren, um die IP-Adresse des VPN-Geräts für Contoso-LNet-US zu konfigurieren.

###VNet-Gateway-Schlüssel festlegen

Die Vnet-Gateways verwenden einen gemeinsamen Schlüssel zum Authentifizieren von Verbindungen zwischen den virtuellen Netzwerken. Der Schlüssel kann nicht aus dem Azure-Portal konfiguriert werden. Sie müssen PowerShell oder .NET SDK verwenden.

**Festlegen der Schlüssel**

1. Öffnen Sie von Ihrer Arbeitsstation **Windows PowerShell ISE** oder die Windows PowerShell-Konsole.
2. Aktualisieren Sie die Parameter in diesem Skript und führen Sie es aus:

		Add-AuzreAccount
		Select-AzureSubscription -[AzureSubscriptionName]
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##Überprüfen Sie die VPN-Verbindung 

Ohne VMs für die VNets bereitgestellt wurden, können Sie das die visuelle Darstellung des virtuellen Netzwerks der VNet-Dashboard-Seite im Azure-Portal verwenden, um den Status der Verbindung zu überprüfen:

![HDInsight HBase Replikation virtuelles Netzwerk Verbindungsstatus][img-vpn-status]
  



##Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, eine VPN-Verbindung zwischen zwei virtuellen Netzwerken in Azure zu konfigurieren. Die anderen zwei Artikel in dieser Serie behandeln:

- [Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-geo-replication-dns]
- [Konfigurieren von HBase-Georeplikation][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: http://manage.windowsazure.com


[powershell-install]: ../install-configure-powershell



[hdinsight-hbase-replication]: ../hdinsight-hbase-geo-replication/
[hdinsight-hbase-dns]: ../hdinsight-hbase-geo-replication-configure-DNS/


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.LNet.diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.status.png

<!---HONumber=July15_HO2-->