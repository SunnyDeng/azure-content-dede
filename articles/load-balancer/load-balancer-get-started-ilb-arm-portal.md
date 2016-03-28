<properties 
   pageTitle="Erste Schritte zum Erstellen eines internen Lastenausgleichs in Resource Manager mit dem Azure-Portal | Microsoft Azure"
   description="Erfahren Sie, wie Sie in Resource Manager mit dem Azure-Portal einen internen Lastenausgleich erstellen."
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
   ms.date="03/04/2016"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines internen Lastenausgleich im Azure-Portal

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Erste Schritte zum Erstellen eines internen Lastenausgleich mit dem Azure-Portal	

Führen Sie zum Erstellen eines internen Lastenausgleichs über das Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie oben links auf dem Bildschirm auf **Neu** > **Netzwerk** > **Lastenausgleich**.
4. Geben Sie auf dem Blatt **Lastenausgleich erstellen** einen **Namen** für den Lastenausgleich ein.
5. Klicken Sie unter **Schema** auf **Intern**.
5. Klicken Sie auf **Virtuelles Netzwerk**, und wählen Sie dann das virtuelle Netzwerk aus, in dem Sie den Lastenausgleich erstellen möchten.

>[AZURE.NOTE] Wenn das virtuelle Netzwerk, das Sie verwenden möchten, nicht angezeigt wird, überprüfen Sie den **Speicherort**, den Sie für den Lastenausgleich verwenden, und ändern Sie ihn entsprechend.

6. Klicken Sie auf **Subnetz**, und wählen Sie dann das Subnetz aus, in dem Sie den Lastenausgleich erstellen möchten.
6. Klicken Sie unter **IP-Adresszuweisung** auf **Dynamisch** oder **Statisch**, je nachdem, ob die IP-Adresse für den Lastenausgleich fest (statisch) sein soll.

>[AZURE.NOTE] Wenn Sie eine statische IP-Adresse verwenden möchten, müssen Sie eine Adresse für den Lastenausgleich angeben.
    
7. Geben Sie unter **Ressourcengruppe** den Namen einer neuen Ressourcengruppe für den Lastenausgleich ein, oder klicken Sie auf **Vorhandene auswählen**, und wählen Sie eine vorhandene Ressourcengruppe aus. 
8. Klicken Sie auf **Erstellen**. 

## Konfigurieren von Lastenausgleichsregeln 

Navigieren Sie nach der Erstellung des Lastenausgleichs zur Lastenausgleichsressource, um sie zu konfigurieren. Sie müssen zuerst einen Back-End-Adresspool und einen Test konfigurieren, bevor Sie eine Lastenausgleichsregel konfigurieren.

### Schritt 1

Konfigurieren eines Back-End-Pools:

1. Klicken Sie im Azure-Portal auf **Durchsuchen** > **Lastenausgleichsmodule**, und klicken Sie dann auf den Lastenausgleich, den Sie soeben erstellt haben.
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Back-End-Pools**. 
3. Klicken Sie auf dem Blatt **Tests** auf **Hinzufügen**.
4. Geben Sie auf dem Blatt **Back-End-Pool hinzufügen** einen **Namen** für den Back-End-Pool ein, und klicken Sie dann auf **OK**.

### Schritt 2 

Konfigurieren eines Tests:
 
1. Klicken Sie im Azure-Portal auf **Durchsuchen** > **Lastenausgleichsmodule**, und klicken Sie dann auf den Lastenausgleich, den Sie soeben erstellt haben.
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Tests**. 
3. Klicken Sie auf dem Blatt **Back-End-Adresspools** auf **Hinzufügen**.
4. Geben Sie auf dem Blatt **Test hinzufügen** einen **Namen** für den Test ein.
5. Wählen Sie unter **Protokoll** entweder **HTTP** (für Websites) oder **TCP** (für andere TCP-basierte Anwendungen) aus.
6. Geben Sie unter **Port** den Port an, der beim Zugreifen auf den Test verwendet werden soll.
7. Geben Sie unter **Pfad** (nur für HTTP-Tests) den Pfad an, der als Test verwendet werden soll.
4. Geben Sie unter **Intervall** an, wie häufig die Anwendung getestet werden soll.
5. Geben Sie unter **Fehlerschwellenwert** an, wie viele Versuche fehlschlagen sollen, bevor die Back-End-VM als „fehlerhaft“ markiert wird.
5. Klicken Sie zum Erstellen des Tests auf **OK**.

### Schritt 3

Konfigurieren von Lastenausgleichsregeln:

1. Klicken Sie im Azure-Portal auf **Durchsuchen** > **Lastenausgleichsmodule**, und klicken Sie dann auf den Lastenausgleich, den Sie soeben erstellt haben.
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Lastenausgleichsregeln**. 
3. Klicken Sie auf dem Blatt **Lastenausgleichsregeln** auf **Hinzufügen**.
4. Geben Sie auf dem Blatt **Lastenausgleichsregel hinzufügen** einen **Namen** für die Regel ein.
5. Wählen Sie unter **Protokoll** entweder **HTTP** (für Websites) oder **TCP** (für andere TCP-basierte Anwendungen) aus.
6. Geben Sie unter **Port** den Port an, mit dem Clients im Lastenausgleich eine Verbindung herstellen.
7. Geben Sie unter **Back-End-Port** den Port an, der im Back-End-Pool verwendet werden soll (in der Regel sind der Lastenausgleichsport und der Back-End-Port identisch).
8. Wählen Sie unter **Back-End-Pool** den Back-End-Pool aus, den Sie soeben erstellt haben.
9. Wählen Sie unter **Sitzungspersistenz** aus, wie Sitzungen beibehalten werden sollen.
10. Geben Sie unter **Leerlauftimeout (Minuten)** das Leerlauftimeout an.
11. Klicken Sie unter **Floating IP (Direct Server Return)** auf **Deaktiviert** oder **Aktiviert**.
12. Klicken Sie auf **OK**.
 
## Nächste Schritte

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0316_2016-->