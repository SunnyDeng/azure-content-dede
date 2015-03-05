<properties 
	pageTitle="Lastenausgleich für Azure-Infrastrukturdienste" 
	description="Beschreibt zwei unterschiedliche Typen des durch Azure unterstützten Lastenausgleichs: Lastenausgleich für Clouddienste und Azure Traffic Manager für Clientdatenverkehr." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="cherylmc"/>


#Lastenausgleich für Azure-Infrastrukturdienste#

Für Azure-Infrastrukturdienste kann Lastenausgleich auf zwei Ebenen genutzt werden:

- **DNS-Ebene**:  Lastenausgleich für Datenverkehr in folgende Richtungen: zu unterschiedlichen Clouddiensten in unterschiedlichen Rechenzentren, zu unterschiedlichen Azure-Websites in unterschiedlichen Rechenzentren oder zu externen Endpunkten. Für diese Form des Lastenausgleichs werden der Traffic Manager und die Roundrobin-Methode verwendet.
- **Netzwerk-Ebene**:  Lastenausgleich für eingehenden Internetdatenverkehr zu unterschiedlichen virtuellen Computern eines Clouddiensts oder Lastenausgleich für den Datenverkehr zwischen virtuellen Computern in einem Clouddienst oder virtuellen Netzwerk. Für diese Aufgaben wird das Azure-Lastenausgleichsmodul verwendet.

##Traffic Manager-Lastenausgleich für Clouddienste und Websites##

Mit Azure Traffic Manager können Sie die Verteilung des Benutzerdatenverkehrs an Endpunkte steuern, z. B. an Clouddienste, Websites, externe Websites und andere Traffic Manager-Profile. Die Funktionsweise von Traffic Manager basiert darauf, dass Sie ein intelligentes Richtlinienmodul auf DNS-Abfragen (Domain Name System) von Domänennamen Ihrer Internetressourcen anwenden. Die Clouddienste oder Websites können in verschiedenen Rechenzentren auf der ganzen Welt ausgeführt werden. 

Sie müssen entweder REST oder Windows PowerShell zur Konfiguration externer Endpunkte oder Traffic Manager-Profile als Endpunkte verwenden. 

Azure Traffic Manager verwendet drei Lastenausgleichsmethoden, um den Datenverkehr zu verteilen:

- **Failover**:  Verwenden Sie diese Methode, wenn Sie einen primären Endpunkt für den gesamten Datenverkehr verwenden, aber Sicherungen bereitstellen möchten, falls der primäre Endpunkt nicht mehr verfügbar sein sollte.
- **Leistung**:  Verwenden Sie diese Methode, wenn sich die Endpunkte an unterschiedlichen geografischen Standorten befinden und anfordernde Clients den "nächstgelegenen" Endpunkt (im Hinblick auf die geringste Latenzzeit) verwenden sollen.
- **Round-Robin:** Verwenden Sie diese Methode, wenn Sie die Last auf eine Reihe von Clouddiensten im gleichen Rechenzentrum oder auf Clouddienste oder Websites in verschiedenen Rechenzentren verteilen möchten.

Weitere Informationen finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](http://msdn.microsoft.com/library/azure/dn339010.aspx).

Die folgende Abbildung zeigt ein Beispiel für die Roundrobin-Lastenausgleichsmethode, bei der der Datenverkehr zwischen unterschiedlichen Clouddiensten verteilt wird.

![loadbalancing](./media/load-balancing-vms/TMSummary.png)

Im Folgenden die grundlegende Vorgehensweise:

1.	Ein Internetclient fragt einen Domänennamen ab, der einem Webdienst entspricht.
2.	DNS leitet die Namensabfrage an den Traffic Manager weiter.
3.	Traffic Manager wählt den nächsten Clouddienst in der Round-Robin-Liste und sendet den DNS-Namen zurück. Der DNS-Server des Internetclients löst den Namen in eine IP-Adresse auf und sendet diese an den Internetclient.
4.	Der Internetclient wählt eine Verbindung mit dem Clouddienst von Traffic Manager aus.

Weitere Informationen finden Sie unter [Traffic Manager](http://msdn.microsoft.com/library/azure/hh745750.aspx).

## Azure-Lastenausgleich für virtuelle Computer ##

Virtuelle Computer im selben Clouddienst oder virtuellen Netzwerk können über ihre privaten IP-Adressen direkt miteinander kommunizieren. Computer und Dienste außerhalb des Clouddiensts oder virtuellen Netzwerks können nur mit virtuellen Computern in einem Clouddienst oder virtuellen Netzwerk mit einem konfigurierten Endpunkt kommunizieren. Ein Endpunkt ist eine Zuordnung einer öffentlichen IP-Adresse und eines Ports zu der privaten IP-Adresse und dem Port eines virtuellen Computers oder einer Webrolle innerhalb eines Azure-Clouddiensts.

Das Azure-Lastenausgleichsmodul verteilt bestimmte eingehende Datenverkehrsdaten nach dem Zufallsprinzip auf mehrere virtuelle Computer oder Dienste. Diese Konfiguration wird als Gruppe mit Lastenausgleich bezeichnet. Sie können zum Beispiel die Netzwerklast von Webanfragen auf mehrere Webserver oder Webrollen verteilen.

Die folgende Abbildung zeigt einen Endpunkt für Standard-Datenverkehr (unverschlüsselt) mit Lastenausgleich, der von drei virtuellen Computern für den öffentlichen und privaten TCP-Port 80 genutzt wird. Diese drei virtuellen Computer bilden einen Satz mit Lastenausgleich.

![loadbalancing](./media/load-balancing-vms/LoadBalancing.png)

Weitere Informationen finden Sie unter [Azure-Lastenausgleich](http://msdn.microsoft.com/library/azure/dn655058.aspx). Die Schritte zum Erstellen einer Gruppe mit Lastenausgleich finden Sie unter [Konfigurieren einer Gruppe mit Lastenausgleich](http://msdn.microsoft.com/library/azure/dn655055.aspx).

Azure ist auch in der Lage, Lasten innerhalb eines Clouddiensts oder virtuellen Netzwerks auszugleichen. Diese Methode wird als interner Lastenausgleich bezeichnet und kann wie folgt verwendet werden:

- Lastenausgleich zwischen Servern auf unterschiedlichen Ebenen einer Multi-Tier-Anwendung (beispielsweise zwischen der Web- und Datenbankebene).
- Lastenausgleich für Branchenanwendungen (LOB-Anwendungen), die in Azure gehostet werden, ohne dass zusätzliche Hardware oder Software für den Lastenausgleich erforderlich ist. 
- Einschließen von lokalen Servern in die Gruppe der Computer, für deren Datenverkehr Lastenausgleich stattfindet.

Vergleichbar mit dem Azure-Lastenausgleich ist auch ein interner Lastenausgleich möglich, indem eine interne Gruppe mit Lastenausgleich konfiguriert wird. 

Die folgende Abbildung zeigt ein Beispiel eines internen Endpunkts mit Lastenausgleich für eine Branchenanwendung (LOB-Anwendung), die in einem standortübergreifenden virtuellen Netzwerk von drei virtuellen Computern gemeinsam genutzt wird. 

![loadbalancing](./media/load-balancing-vms/LOBServers.png)

Weitere Informationen finden Sie unter [Interner Lastenausgleich](http://msdn.microsoft.com/library/azure/dn690121.aspx). Die Schritte zum Erstellen einer Gruppe mit Lastenausgleich finden Sie unter [Konfigurieren einer internen Gruppe mit Lastenausgleich](http://msdn.microsoft.com/library/azure/dn690125.aspx).

<!-- LINKS -->


<!--HONumber=45--> 
