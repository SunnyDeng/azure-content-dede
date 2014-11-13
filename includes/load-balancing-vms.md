<properties title="Lastenausgleich f&uuml;r Azure-Infrastrukturdienste" pageTitle="Lastenausgleich f&uuml;r Azure-Infrastrukturdienste" description="Beschreibt die Funktionen zum Ausf&uuml;hren des Lastenausgleichs mit Traffic Manager und dem Lastenausgleich." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="cherylmc" videoId="" scriptId="" manager="adinah" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="cherylmc" />

# Lastenausgleich für Azure-Infrastrukturdienste

Für Azure-Infrastrukturdienste kann Lastenausgleich auf zwei Ebenen genutzt werden:

-   **DNS-Ebene**: Lastenausgleich für Datenverkehr in folgende Richtungen: zu unterschiedlichen Cloud-Diensten in unterschiedlichen Rechenzentren, zu unterschiedlichen Azure-Websites in unterschiedlichen Rechenzentren oder zu externen Endpunkten. Für diese Form des Lastenausgleichs werden der Traffic Manager und die Roundrobin-Methode verwendet.
-   **Netzwerkebene**: Lastenausgleich für eingehenden Internetdatenverkehr zu unterschiedlichen virtuellen Computern eines Cloud-Diensts oder Lastenausgleich für den Datenverkehr zwischen virtuellen Computern in einem Cloud-Dienst oder virtuellen Netzwerk. Für diese Aufgaben wird das Azure-Lastenausgleichsmodul verwendet.

## Traffic Manager-Lastenausgleich für Cloud-Dienste und Websites

Mit Azure Traffic Manager können Sie die Verteilung des Benutzerdatenverkehrs an Endpunkte steuern, z. B. an Cloud-Dienste, Websites externe Websites und andere Traffic Manager-Profile. Die Funktionsweise von Traffic Manager basiert darauf, dass Sie ein intelligentes Richtlinienmodul auf DNS-Abfragen (Domain Name System) von Domänennamen Ihrer Internetressourcen anwenden. Die Cloud-Dienste oder Websites können in verschiedenen Rechenzentren auf der ganzen Welt ausgeführt werden.

Sie müssen entweder REST oder Windows PowerShell zur Konfiguration externer Endpunkte oder Traffic Manager-Profile als Endpunkte verwenden.

Azure Traffic Manager verwendet drei Lastenausgleichsmethoden, um den Datenverkehr zu verteilen:

-   **Failover**: Verwenden Sie diese Methode, wenn Sie einen primären Endpunkt für den gesamten Datenverkehr verwenden, aber Sicherungen bereitstellen möchten, falls der primäre Endpunkt nicht mehr verfügbar sein sollte.
-   **Leistung**: Verwenden Sie diese Methode, wenn sich die Endpunkte an unterschiedlichen geografischen Standorten befinden und anfordernde Clients den "nächstgelegenen" Endpunkt (im Hinblick auf die geringste Latenzzeit) verwendet sollen.
-   **Roundrobin:** Verwenden Sie diese Methode, wenn Sie die Last auf eine Reihe von Cloud-Diensten im gleichen Rechenzentrum oder auf Cloud-Dienste oder Websites in verschiedenen Rechenzentren verteilen möchten.

Weitere Informationen finden Sie unter [Traffic Manager-Lastenausgleichsmethoden][Traffic Manager-Lastenausgleichsmethoden].

Die folgende Abbildung zeigt ein Beispiel für die Roundrobin-Lastenausgleichsmethode, bei der der Datenverkehr zwischen unterschiedlichen Cloud-Diensten verteilt wird.

![Lastenausgleich][Lastenausgleich]

Im Folgenden die grundlegende Vorgehensweise:

1.  Ein Internetclient fragt einen Domänennamen ab, der einem Webdienst entspricht.
2.  DNS leitet die Namensabfrage an den Traffic Manager weiter.
3.  Der Traffic Manager gibt den DNS-Namen des Cloud-Diensts in der Roundrobinliste zurück. Der DNS-Server des Internetclients löst den Namen in eine IP-Adresse auf und sendet diese an den Internetclient.
4.  Der Internetclient stellt eine Verbindung mit dem ausgewählten Cloud-Dienst her.

Weitere Informationen finden Sie unter [Traffic Manager][Traffic Manager].

## Azure-Lastenausgleich für virtuelle Computer

Virtuelle Computer im selben Cloud-Dienst oder virtuellen Netzwerk können über ihre privaten IP-Adressen direkt miteinander kommunizieren. Computer und Dienste außerhalb des Cloud-Diensts oder virtuellen Netzwerks können nur mit virtuellen Computern in einem Cloud-Dienst oder virtuellen Netzwerk mit einem konfigurierten Endpunkt kommunizieren. Ein Endpunkt ist eine Zuordnung einer öffentlichen IP-Adresse und eines Ports zu der privaten IP-Adresse und dem Port eines virtuellen Computers oder einer Webrolle innerhalb eines Azure-Cloud-Diensts.

Das Azure-Lastenausgleichsmodul verteilt bestimmte eingehende Datenverkehrsdaten nach dem Zufallsprinzip auf mehrere virtuelle Computer oder Dienste. Diese Konfiguration wird als Gruppe mit Lastenausgleich bezeichnet. Sie können zum Beispiel die Netzwerklast von Webanfragen auf mehrere Webserver oder Webrollen verteilen.

Die folgende Abbildung zeigt einen Endpunkt mit Lastenausgleich für standardmäßigen (nicht verschlüsselten) Webdatenverkehr, der zwischen drei virtuellen Computern für den öffentlichen und privaten TCP-Port 80 aufgeteilt wird. Diese drei virtuellen Computer befinden sich in einer Gruppe mit Lastenausgleich.

![Lastenausgleich][1]

Weitere Informationen finden Sie unter [Azure-Lastenausgleichsmodul][Azure-Lastenausgleichsmodul]. Die Schritte zum Erstellen einer Gruppe mit Lastenausgleich finden Sie unter [Konfigurieren einer Gruppe mit Lastenausgleich][Konfigurieren einer Gruppe mit Lastenausgleich].

Azure ist auch in der Lage, Lasten innerhalb eines Cloud-Diensts oder virtuellen Netzwerks auszugleichen. Diese Methode wird als interner Lastenausgleich bezeichnet und kann wie folgt verwendet werden:

-   Lastenausgleich zwischen Servern auf unterschiedlichen Ebenen einer Multi-Tier-Anwendung (beispielsweise zwischen der Web- und Datenbankebene).
-   Lastenausgleich für Branchenanwendungen (LOB-Anwendungen), die in Azure gehostet werden, ohne dass zusätzliche Hardware oder Software für den Lastenausgleich erforderlich ist.
-   Einschließen von lokalen Servern in die Gruppe der Computer, für deren Datenverkehr Lastenausgleich stattfindet.

Vergleichbar mit dem Azure-Lastenausgleich ist auch ein interner Lastenausgleich möglich, indem eine interne Gruppe mit Lastenausgleich konfiguriert wird.

Die folgende Abbildung zeigt ein Beispiel eines internen Endpunkts mit Lastenausgleich für eine Branchenanwendung (LOB-Anwendung), die in einem standortübergreifenden virtuellen Netzwerk von drei virtuellen Computern gemeinsam genutzt wird.

![Lastenausgleich][2]

Weitere Informationen finden Sie unter [Interner Lastenausgleich][Interner Lastenausgleich]. Die Schritte zum Erstellen einer Gruppe mit Lastenausgleich finden Sie unter [Konfigurieren einer internen Gruppe mit Lastenausgleich][Konfigurieren einer internen Gruppe mit Lastenausgleich].

<!-- LINKS -->

  [Traffic Manager-Lastenausgleichsmethoden]: http://msdn.microsoft.com/de-de/library/azure/dn339010.aspx
  [Lastenausgleich]: ./media/load-balancing-vms/TMSummary.png
  [Traffic Manager]: http://msdn.microsoft.com/de-de/library/azure/hh745750.aspx
  [1]: ./media/load-balancing-vms/LoadBalancing.png
  [Azure-Lastenausgleichsmodul]: http://msdn.microsoft.com/de-de/library/azure/dn655058.aspx
  [Konfigurieren einer Gruppe mit Lastenausgleich]: http://msdn.microsoft.com/de-de/library/azure/dn655055.aspx
  [2]: ./media/load-balancing-vms/LOBServers.png
  [Interner Lastenausgleich]: http://msdn.microsoft.com/de-de/library/azure/dn690121.aspx
  [Konfigurieren einer internen Gruppe mit Lastenausgleich]: http://msdn.microsoft.com/de-de/library/azure/dn690125.aspx
