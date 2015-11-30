<properties
   pageTitle="Deaktivieren, Aktivieren oder Löschen eines Traffic Manager-Profils | Microsoft Azure"
   description="In diesem Artikel wird erläutert, wie Sie mit Traffic Manager-Profilen arbeiten."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# Deaktivieren, Aktivieren oder Löschen eines Profils


Sie können ein vorhandenes Traffic Manager-Profil so deaktivieren, dass keine Benutzeranforderungen mehr an die konfigurierten Endpunkte verwiesen werden. Wenn Sie ein Traffic Manager-Profil deaktivieren, bleiben das Profil selbst und die Informationen im Profil erhalten und können auf der Traffic Manager-Benutzeroberfläche bearbeitet werden. Wenn Sie das Profil erneut aktivieren möchten, können Sie dies problemlos im Verwaltungsportal tun, woraufhin die Weiterleitung wird aufgenommen wird. Wenn Sie ein Traffic Manager-Profil im Verwaltungsportal erstellen, wird es automatisch aktiviert.

## So deaktivieren Sie ein Profil

1. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass der entsprechende Eintragstyp und Zeiger auf einen anderen Namen oder die IP-Adresse eines bestimmten Speicherorts im Internet verwendet werden. Ändern Sie also den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass er nicht mehr einen CNAME-Ressourceneintrag verwendet, der auf den Domänennamen Ihres Traffic Manager-Profils verweist.
1. Der Datenverkehr wird nicht mehr über die Traffic Manager-Profileinstellungen an die Endpunkte weitergeleitet.
1. Wählen Sie das Profil aus, das Sie deaktivieren möchten. Um das Profil auszuwählen, markieren Sie das Profil auf der Seite "Traffic Manager" durch Klicken auf die Spalte neben dem Profilnamen. Klicken Sie nicht auf den Namen des Profils oder den Pfeil neben dem Namen, da Sie dadurch zur Einstellungsseite für das Profil gelangen.
1. Klicken Sie nach Auswahl des Profils am unteren Rand der Seite auf "Deaktivieren".

## So aktivieren Sie ein Profil

1. Wählen Sie das Profil aus, das Sie aktivieren möchten. Um das Profil auszuwählen, markieren Sie das Profil auf der Seite "Traffic Manager" durch Klicken auf die Spalte neben dem Profilnamen. Klicken Sie nicht auf den Namen des Profils oder den Pfeil neben dem Namen, da Sie dadurch zur Einstellungsseite für das Profil gelangen.
1. Klicken Sie nach Auswahl des Profils am unteren Rand der Seite auf "Aktivieren".
1. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass der mit CNAME-Eintragstyp verwendet wird, der den Domänennamen Ihres Unternehmens dem Domänennamen Ihres Traffic Manager-Profils zuordnet. Weitere Informationen dazu finden Sie unter [Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](traffic-manager-point-internet-domain.md).
1. Der Datenverkehr wird wieder an die Endpunkte weitergeleitet.

## Löschen eines Profils


1. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass er nicht mehr einen CNAME-Ressourceneintrag verwendet, der auf den Domänennamen Ihres Traffic Manager-Profils verweist.
1. Wählen Sie das Profil aus, das Sie löschen möchten. Um das Profil auf der Seite "Traffic Manager" auszuwählen, markieren Sie das Profil, 
1. indem Sie auf die Spalte neben dem Profil klicken. Klicken Sie nicht auf den Namen des Profils oder den Pfeil neben dem Namen, da Sie dadurch zur Einstellungsseite für das Profil gelangen.
1. Klicken Sie nach Auswahl des Profils am unteren Rand der Seite auf "Löschen".

## Nächste Schritte

[Deaktivieren oder Aktivieren eines Traffic Manager-Endpunkts](disable-or-enable-an-endpoint.md)

[Konfigurieren der Routingmethode „Failover“](traffic-manager-configure-failover-routing-method.md)

[Konfigurieren der Routingmethode „Roundrobin“](traffic-manager-configure-round-robin-routing-method.md)

[Konfigurieren der Routingmethode „Performance“](traffic-manager-configure-performance-routing-method.md)

[Problembehandlung beim Status „Heruntergestuft“ in Traffic Manager](traffic-manager-troubleshooting-degraded.md)

<!---HONumber=Nov15_HO4-->