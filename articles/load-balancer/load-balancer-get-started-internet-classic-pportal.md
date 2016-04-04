<properties 
   pageTitle="Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff in einem klassischen Bereitstellungsmodell im Azure-Portal | Microsoft Azure"
   description="Erfahren Sie, wie Sie im Azure-Portal einen Load Balancer mit Internetzugriff in einem klassischen Bereitstellungsmodell erstellen"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff (klassisch) im Azure-Portal

[AZURE.INCLUDE [Load-Balancer-Get-Started-Internet-Classic-Selectors-include.MD](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können auch [erfahren, wie Sie mit dem Azure-Ressourcen-Manager einen Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-arm-ps.md).

 
[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Erste Schritte zum Erstellen eines Load Balancer-Endpunkts mithilfe des Azure-Portals	

Führen Sie zum Erstellen eines (klassischen) Load Balancer-Bereitstellungsmodells mit Internetzugriff im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.

2. Rufen Sie das Blatt „Virtuelle Computer (Klassisch)“ auf, und wählen Sie einen virtuellen Computer aus.

3. Wählen Sie auf dem Blatt „Zusammenfassung“ für virtuelle Computer die Option „Alle Einstellungen“.

4. Klicken Sie auf „Lastenausgleichsgruppen“.

5. Klicken Sie zum Erstellen eines neuen Load Balancers oben auf dem Blatt für Gruppen mit Lastenausgleich auf das Symbol zum Verknüpfen.

6. Wählen Sie für den Load Balancer mit Internetzugriff als Typ für die Gruppe mit Lastenausgleich die Option „Öffentlich“.

7. Klicken Sie auf „Erforderliche Einstellungen konfigurieren“, um „Gruppe mit Lastenausgleich auswählen“ zu öffnen, und klicken Sie dann auf „Gruppe mit Lastenausgleich erstellen“.

8. Erstellen Sie auf dem Blatt „Gruppe mit Lastenausgleich erstellen“ einen Namen für die Load Balancer-Gruppe. Geben Sie den Namen, den öffentlichen Port, das Testprotokoll und den Testport ein.

9. Ändern Sie ggf. Testintervall und Wiederholungsversuche.

10. (Optional) Sie können auf dem Blatt für die Erstellung der Load Balancer-Gruppe auch Zugriffssteuerungsregeln konfigurieren.

11. Klicken Sie auf „OK“, um wieder das Blatt „Gruppe mit Lastenausgleich verknüpfen“ aufzurufen.

12. Klicken Sie auf „OK“, und warten Sie, bis die neue Load Balancer-Ressource auf dem Blatt „Load Balancer-Gruppen“ angezeigt wird.
 
## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->