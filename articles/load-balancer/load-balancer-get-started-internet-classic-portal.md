<properties 
   pageTitle="Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff in einem klassischen Bereitstellungsmodell im Azure-Portal | Microsoft Azure"
   description="Erfahren Sie, wie Sie im Azure-Portal einen Load Balancer mit Internetzugriff in einem klassischen Bereitstellungsmodell erstellen"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff (klassisch) im Azure-Portal

[AZURE.INCLUDE [Load-Balancer-Get-Started-Internet-Classic-Selectors-include.MD](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können auch [erfahren, wie Sie mit dem Azure-Ressourcen-Manager einen Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## Einrichten des Lastenausgleichs für Internetverbindungen für virtuelle Computer

Um Lastenausgleich für Netzwerkverkehr aus dem Internet auf virtuellen Computern eines Clouddiensts auszuführen, müssen Sie eine Gruppe mit Lastenausgleich erstellen. Dieses Verfahren setzt voraus, dass Sie die virtuellen Computer bereits erstellt haben und diese sich im selben Clouddienst befinden.

**So konfigurieren Sie eine Gruppe mit Lastenausgleich für virtuelle Computer**

1. Klicken Sie im Azure-Portal auf **Virtuelle Computer** und anschließend auf den Namen eines virtuellen Computers in der Gruppe mit Lastenausgleich.
2.	Klicken Sie auf **Endpunkte** und anschließend auf **Hinzufügen**.

4.	Klicken Sie auf der Seite **Endpunkt zu einem virtuellen Computer hinzufügen** auf den rechten Pfeil.

4.	Gehen Sie auf der Seite **Geben Sie die Details des Endpunktes ein** folgendermaßen vor:
	- Geben Sie unter **Name** einen Namen für den Endpunkt ein, oder wählen Sie einen der vordefinierten Endpunkte für gängige Protokolle aus der Liste aus.
	-  Wählen Sie unter **Protokoll** das Protokoll aus, das für den Endpunkttyp erforderlich ist: TCP oder UDP.
 	-  Geben Sie unter **Öffentlicher Port** und "Privater Port" die Portnummern ein, die der virtuelle Computer verwenden soll. Sie können die Regeln für privaten Port und Firewall auf dem virtuellen Computer verwenden, um Datenverkehr entsprechend Ihren Anforderungen umzuleiten. Der private Port kann mit dem öffentlichen Port identisch sein. Für einen Endpunkt für Webverkehr (HTTP) könnten Sie zum Beispiel Port 80 sowohl für den öffentlichen als auch für den privaten Port verwenden.

5.	Klicken Sie auf **Satz mit Lastenausgleich erstellen** und klicken Sie auf den Pfeil nach rechts.

6.	Geben Sie auf der Seite **Gruppe mit Lastenausgleich konfigurieren** einen Namen für die Gruppe mit Lastenausgleich ein, und weisen Sie dann die Werte für das Testverhalten des Azure-Lastenausgleichsmoduls zu. Das Lastenausgleichsmodul führt Tests durch, um zu ermitteln, ob die virtuellen Computer in einem Satz mit Lastenausgleich empfangsbereit für eingehenden Datenverkehr sind.

7.	Klicken Sie auf das Häkchen, um den Endpunkt mit Lastenausgleich zu erstellen. In der Spalte **Name des Satzes mit Lastenausgleich** auf der Seite **Endpunkt** für den virtuellen Computer wird der Eintrag **Ja** angezeigt.

8.	Klicken Sie im Portal auf **Virtuelle Computer**, klicken Sie auf den Namen eines weiteren virtuellen Computers in der Gruppe mit Lastenausgleich, klicken Sie auf **Endpunkte** und dann auf **Hinzufügen**.

9.	Klicken Sie auf der Seite **Endpunkt zu virtuellem Computer hinzufügen** auf **Endpunkt zu einer vorhandenen Gruppe mit Lastenausgleich hinzufügen**, wählen Sie den Namen der Gruppe mit Lastenausgleich aus, und klicken Sie auf den Pfeil nach rechts.

10.	Geben Sie auf der Seite **Geben Sie die Details des Endpunktes ein** einen Namen für den Endpunkt ein, und klicken Sie auf das Häkchen. Wiederholen Sie die Schritte 8 bis 10 für die weiteren virtuellen Computer in der Gruppe mit Lastenausgleich.



## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-internal-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->