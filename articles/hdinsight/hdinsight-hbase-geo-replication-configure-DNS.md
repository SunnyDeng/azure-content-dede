<properties 
   pageTitle="Konfigurieren des DNS zwischen zwei virtuellen Netzwerken in Azure | Microsoft Azure" 
   description="Erfahren Sie, wie Sie VPN-Verbindungen und die Domänennamenauflösung zwischen zwei virtuellen Netzwerken sowie die HBase-Georeplikation konfigurieren." 
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
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 


Erfahren Sie, wie Sie DNS-Server zu virtuellen Netzwerken in Azure hinzufügen und konfigurieren, um die Namensauflösung innerhalb und außerhalb der virtuellen Netzwerke zu behandeln.

Dieses Lernprogramm ist der zweite Teil der [Reihe][hdinsight-hbase-geo-replication] zum Erstellen von HBase Georeplikation:

- [Konfigurieren einer VPN-Konnektivität zwischen zwei virtuellen Netzwerken][hdinsight-hbase-geo-replication-vnet]
- Konfigurieren von DNS für virtuelle Netzwerke (dieses Lernprogramm)
- [Konfigurieren von HBase-Georeplikation][hdinsight-hbase-geo-replication]


Das folgende Diagramm veranschaulicht die beiden virtuellen Netzwerke, die Sie in [Konfigurieren Sie eine VPN-Konnektivität zwischen zwei virtuellen Netzwerken][hdinsight-hbase-geo-replication-vnet] erstellt haben:

![HDInsight HBase Replikation virtuelles Netzwerkdiagramm][img-vnet-diagram]

##Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

		Add-AzureAccount

	Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

		Select-AzureSubscription <AzureSubscriptionName>

- **Zwei virtuelle Netzwerke in Azure mit VPN-Konnektivität**. Anweisungen finden Sie in [Konfigurieren einer VPN-Verbindung zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE]Azure Dienstnamen und die Namen der virtuellen Computer müssen eindeutig sein. Der in diesem Lernprogramm verwendete Name ist Contoso-[Azure Service/VM name]-[EU/US]. Contoso-VNet-EU ist z. B. das virtuelle Azure-Netzwerk im Rechenzentrum Nordeuropa; Contoso-DNS-US ist der DNS-Server VM im Datencenter im Osten der USA. Sie müssen sich Ihre eigenen Namen ausdenken.
 
 
##Erstellen Sie virtuelle Computer in Azure, die als DNS-Server verwendet werden sollen

**So erstellen Sie einen virtuellen Computer innerhalb Contoso-VNet-EU namens Contoso-DNS-EU**

1.	Klicken Sie auf **NEU** > **BERECHNEN** > **VIRTUELLER COMPUTER** > **AUS KATALOG**.
2.	Wählen Sie **Windows Server 2012 R2 Datencenter** aus.
3.	Geben Sie Folgendes ein:
	- **NAME DES VIRTUELLEN COMPUTERS**: Contoso-DNS-EU
	- **NEUER BENUTZERNAME**: 
	- **NEUES KENNWORT**: 
4.	Geben Sie Folgendes ein:
	- **CLOUD-DIENST**: Erstellen Sie einen neuen Cloud-Dienst
	- **REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK**: (Wählen Sie Contoso-VNet-EU aus)
	- **SUBNETZE DES VIRTUELLEN NETZWERKS**: Subnetz-1
	- **SPEICHERKONTO**: Verwenden Sie ein automatisch generiertes Speicherkonto
	
		Der Name des Clouddiensts ist mit dem des virtuellen Computers identisch. In diesem Fall lautet er "Contoso-DNS-EU". Für nachfolgende virtuelle Computer können Sie den gleichen Clouddienst auswählen. Alle virtuellen Computer unter demselben Clouddienst verwenden das gleiche virtuelle Netzwerk und den gleichen Domänensuffix.

		Das Speicherkonto wird zum Speichern der Imagedatei des virtuellen Computers verwendet. 
	- **ENDPUNKTE**: (Scrollen nach unten, und wählen Sie **DNS** aus) 

Ermitteln Sie nach der Erstellung des virtuellen Computers die interne und externe IP.

1.	Klicken Sie auf den Namen des virtuellen Computers, **Contoso-DNS-EU**.
2.	Klicken Sie auf **Dashboard**.
3.	Notieren Sie sich:
	- ÖFFENTLICHE VIRTUELLE IP-ADRESSE
	- INTERNE IP-ADRESSE


**So erstellen Sie einen virtuellen Computer innerhalb Contoso-VNet-US namens Contoso-DNS-US**

- Wiederholen Sie die gleiche Prozedur zum Erstellen eines virtuellen Computers mit den folgenden Werten:
	- NAME DES VIRTUELLEN COMPUTERS: Contoso-DNS-US
	- REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK: Wählen Sie Contoso-VNet-US aus
	- SUBNETZE DES VIRTUELLEN NETZWERKS: Subnetz-1
	- SPEICHERKONTO: Verwenden Sie ein automatisch generiertes Speicherkonto
	- ENDPUNKTE: (DNS auswählen)

##Legen Sie statische IP-Adressen für die beiden virtuellen Computer fest

DNS-Server erfordern statische IP-Adressen. Dieser Schritt kann nicht vom klassischen Azure-Portal aus durchgeführt werden. Sie verwenden dafür Azure PowerShell.

**So konfigurieren Sie statische IP-Adresse für die beiden virtuellen Computer**

1. Öffnen Sie Windows PowerShell ISE.
2. Führen Sie die folgenden cmdlets aus.  

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName ist der Name des Cloud-Dienstes. Da der DNS-Server der erste virtuelle Computer des Cloud-Dienstes ist, entspricht der Name des Cloud-Dienstes dem Namen des virtuellen Computers.

	Sie müssen möglicherweise ServiceName und Name aktualisieren, damit sie mit den bestehenden Namen übereinstimmen.


##Die DNS-Serverrolle zu den beiden virtuellen Computern hinzufügen

**So fügen Sie die DNS-Serverrolle zu Contoso-DNS-EU hinzu**

1.	Klicken Sie im klassischen Azure-Portal auf **Virtuelle Computer** auf der linken Seite. 
2.	Klicken Sie auf **Contoso-DNS-EU**.
3.	Klicken Sie von oben auf **DASHBOARD**.
4.	Klicken Sie auf **CONNECT** von unten, und befolgen Sie die Anweisungen für die Verbindung mit dem virtuellen Computer über RDP.
2.	Klicken Sie in der RDP-Sitzung auf die Windows-Schaltfläche auf der unteren linken Ecke, um die Startseite zu öffnen.
3.	Klicken Sie auf die Kachel **Server-Manager**.
4.	Klicken Sie auf **Rollen und Features hinzufügen**.
5.	Klicken Sie auf **Weiter**.
6.	Wählen Sie **Rollenbasierte oder featurebasierte Installation** und klicken Sie auf **Weiter**.
7.	Wählen Sie Ihre DNS-VM (sie sollte bereits hervorgehoben sein), und klicken Sie dann auf **Weiter**.
8.	Überprüfen Sie den **DNS-Server**.
9.	Klicken Sie auf **Features hinzufügen** und anschließend auf **Weiter**.
10.	Klicken Sie dreimal auf **Weiter**, und klicken Sie dann auf **Installieren**. 

**So fügen Sie die DNS-Serverrolle zu Contoso-DNS-US hinzu**

- Wiederholen Sie die Schritte zum Hinzufügen der DNS-Serverrolle auf **Contoso-DNS-de**.

##Weisen Sie virtuellen Netzwerken DNS-Server zu

**So registrieren Sie die beiden DNS-Server**

1.	Klicken Sie im klassischen Azure-Portal auf **NEU**, **NETWORK SERVICES**, **VIRTUELLES NETZWERK**, **DNS-SERVER REGISTRIEREN**.
2.	Geben Sie Folgendes ein:
	- **NAME**: Contoso-DNS-EU
	- **IP-ADRESSE DES DNS-SERVERS**: 10.1.0.4 – die IP-Adresse muss mit der IP-Adresse des virtuellen Computers vom DNS-Server übereinstimmen.
	 
3.	Wiederholen Sie den Vorgang zum Registrieren von Contoso-DNS-US mit den folgenden Einstellungen:
	- **NAME**: Contoso-DNS-US
	- **IP-ADRESSE DES DNS-SERVERS**: 10.2.0.4

**Den beiden virtuellen Netzwerken die zwei DNS-Server zuweisen**

1.	Klicken Sie auf **Netzwerke** im linken Bereich im klassischen Portal.
2.	Klicken Sie auf **Contoso-VNet-EU**
3.	Klicken Sie auf **KONFIGURIEREN**.
4.	Wählen Sie **Contoso-DNS-EU** im Abschnitt **DNS-Server** aus.
5.	Klicken Sie unten auf der Seite auf **SPEICHERN** und dann zum Bestätigen auf **Ja**.
6.	Wiederholen Sie den Vorgang zum Zuweisen des DNS-Servers **Contoso-DNS-US** zum virtuellen Netzwerk **Contoso-VNet-US**.

Alle virtuellen Computer, die den virtuellen Netzwerken bereitgestellt wurden, müssen neu gestartet werden, damit die DNS-Serverkonfiguration aktualisiert wird.

**So starten Sie die virtuellen Computer neu**

1. Klicken Sie im klassischen Azure-Portal auf **Virtuelle Computer** auf der linken Seite.
2. Klicken Sie auf **Contoso-DNS-EU**.
3. Klicken Sie von oben auf **Dashboard**.
4. Klicken Sie unten auf **NEU STARTEN**.
5. Wiederholen Sie die gleichen Schritte, um **Contoso-DNS-US** neu zu starten.


##Konfigurieren der bedingten DNS-Weiterleitungen

Der DNS-Server kann in jedem virtuellen Netzwerk nur DNS-Namen innerhalb des jeweiligen virtuellen Netzwerks auflösen. Sie müssen eine bedingte Weiterleitung auf den Peer-DNS-Server für Namensauflösungen im virtuellen Peer-Netzwerk konfigurieren.

Um bedingte Weiterleitung zu konfigurieren, müssen Sie die Domänenendungen der beiden DNS-Server kennen. Die DNS-Suffixe können unterschiedlich sein, abhängig von der Konfiguration der Cloud-Dienste, die Sie bei der Erstellung der virtuellen Computer verwendet haben. Für jedes im virtuellen Netzwerk verwendete DNS-Suffix müssen Sie eine bedingte Weiterleitung hinzufügen.

**Finden der Domänenendungen der beiden DNS-Server**

1. RDP in **Contoso-DNS-EU**.
2. Öffnen Sie die Windows PowerShell-Konsole oder die Eingabeaufforderung.
3. Führen Sie **ipconfig** aus und notieren Sie sich **verbindungsspezifische DNS-Endungen**.
4. Schließen Sie die RDP-Sitzung nicht, sie wird weiterhin benötigt. 
5. Wiederholen Sie dieselben Schritte, um die **verbindungsspezifische DNS-Endung** von **Contoso-DNS-US** herauszufinden.


**Konfigurieren von DNS-Weiterleitungen**
 
1.	Von der RDP-Sitzung zu **Contoso-DNS-EU**, klicken Sie auf die Windows-Taste auf der linken unteren Ecke.
2.	Klicken Sie auf **Verwaltungstools**.
3.	Klicken Sie auf **DNS**.
4.	Erweitern Sie im linken Fensterbereich **DSN**, **Contoso-DNS-EU**.
5.	Geben Sie Folgendes ein:
	- **DNS-Domäne**: Geben Sie das DNS-Suffix von Contoso-DNS-US ein. Zum Beispiel: Contoso-DNS-US.b5.internal.cloudapp.net.
	- **IP-Adressen der Masterserver**: Geben Sie 10.2.0.4 ein, dabei handelt es sich um die IP-Adresse von Contoso-DNS-US.
6.	Klicken Sie auf **ENTER** und dann auf **OK**. Sie können nun die IP-Adresse von Contoso-DNS-US von der von Contoso-DNS-EU auflösen.
7.	Wiederholen Sie die Schritte zum Hinzufügen einer DNS-Weiterleitung an den DNS-Dienst auf dem virtuellen Computer von Contoso-DNS-US mit den folgenden Werten:
	- **DNS-Domäne**: Geben Sie das DNS-Suffix von Contoso-DNS-EU ein. 
	- **IP-Adressen der Masterserver**: Geben Sie 10.2.0.4 ein, dabei handelt es sich um die IP-Adresse von Contoso-DNS-EU.

##Testen Sie die Namensauflösung in den virtuellen Netzwerken

Sie können nun die Auflösung von Hostnamen in den virtuellen Netzwerken testen. Ping wird standardmäßig von der Firewall blockiert. Sie können mit nslookup die virtuellen Computer der DNS-Server (Sie müssen FQDN verwenden) in den Peer-Netzwerken auflösen.


##Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Namensauflösungen in virtuellen Netzwerken mit VPN-Verbindungen konfiguriert werden. Die anderen zwei Artikel in dieser Serie behandeln:

- [Konfigurieren einer VPN-Verbindung zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-geo-replication-vnet]
- [Konfigurieren von HBase-Georeplikation][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: ../install-configure-powershell.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---HONumber=AcomDC_1203_2015-->