<properties
   pageTitle="Migrieren des Standort-zu-Standort-VPN zu ExpressRoute"
   description="Dieser Artikel hilft Ihnen dabei, das Standort-zu-Standort-VPN zu ExpressRoute zu migrieren."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> 

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# Verbindungsmigration: von Standort-zu-Standort-VPN zu ExpressRoute

Azure-Kunden können eine Verbindung mit ihrem virtuellen Netzwerk auf zwei Arten herstellen: über ein Standort-zu-Standort-VPN und über ExpressRoute. Die Standort-zu-Standort-VPN-Verbindung erfolgt über das öffentliche Internet. Zum Verschlüsseln des Netzwerkverkehrs wird IPsec verwendet. ExpressRoute ist eine private Verbindung mit Azure. Sie können eine Verbindung mit Azure entweder über einen [Exchange-Anbieter (EXP)](expressroute-exchange-providers.md) oder [Netzwerkdienstanbieter (NSP)](expressroute-network-service-providers.md) herstellen.

Wenn Sie bereits über eine Standort-zu-Standort-VPN-Verbindung mit dem virtuellen Netzwerk verfügen, befolgen Sie die unten stehenden Schritte, um sie zu einer Verbindung über ExpressRoute zu migrieren.

1) Melden Sie sich beim Azure-Portal an.

2) Klicken Sie im Navigationsbereich auf **NETZWERKE**, und klicken Sie auf das virtuelle Netzwerk, das über die VPN-Verbindung verfügt.

3) Klicken Sie auf **DASHBOARD**. Klicken Sie am unteren Rand der Seite auf **GATEWAY LÖSCHEN**, klicken Sie dann auf **JA**.

  Mit diesem Schritt entfernen Sie das Standort-zu-Standort-VPN-Gateway.

4) Sobald im Portal für das virtuelle Netzwerk "DAS GATEWAY WURDE NICHT ERSTELLT" angezeigt wird, klicken Sie auf die Seite **KONFIGURIEREN**.

a) Aktivieren Sie das Kontrollkästchen "ExpressRoute verwenden".

b) Ändern Sie die CIDR für das Gatewaysubnetz in "/28".

![Abonnementfreigabe](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5) Klicken Sie am unteren Rand der Seite auf **SPEICHERN**, klicken Sie dann auf **JA**.

6) Klicken Sie auf **DASHBOARD**. Klicken Sie am unteren Rand der Seite auf **GATEWAY ERSTELLEN**, klicken Sie dann auf **JA**.

 Mit diesem Schritt erstellen Sie das ExpressRoute-Gateway.
 
Nachdem das neue Gateway erstellt wurde, kann das virtuelle Netzwerk eine Verbindung zur ExpressRoute-Verbindung herstellen. Alle virtuellen Computer im virtuellen Netzwerk werden während des gesamten Prozesses ausgeführt. Sie müssen sie nicht verschieben oder herunterfahren.

Gehen Sie zum letzten Schritt im Lernprogramm zur Konfiguration von ExpressRoute über einen [Exchange-Anbieter](expressroute-configuring-exps.md) / [Netzwerkdienstanbieter](expressroute-configuring-nsps.md), um das virtuelle Netzwerk mit der ExpressRoute-Verbindung zu verknüpfen.



<!--HONumber=54-->