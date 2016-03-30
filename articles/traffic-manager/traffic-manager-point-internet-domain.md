<properties
   pageTitle="Verweisen einer Unternehmensinternetdomäne auf eine Traffic Manager-Domäne | Microsoft Azure"
   description="In diesem Artikel erfahren Sie, wie Sie mit Ihrem Unternehmensdomänennamen auf einen Traffic Manager-Domänennamen verweisen."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Verweisen einer Unternehmensinternetdomäne auf eine Azure Traffic Manager-Domäne

Um mit Ihrem Unternehmensdomänennamen auf einen Traffic Manager-Domänennamen zu verweisen, ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server mit dem CNAME-Eintragstyp, der den Domänennamen Ihres Unternehmens dem Domänennamen Ihres Traffic Manager-Profils zuordnet. Sie finden den Traffic Manager-Domänennamen auf der Konfigurationsseite des Traffic Manager-Profils im Abschnitt **Allgemein**.

Um beispielsweise mit dem Unternehmens-Domänennamen www.contoso.com auf den Traffic Manager-Domänennamen contoso.trafficmanager.net zu verweisen, ändern Sie den DNS-Ressourceneintrag wie folgt:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Alle Datenverkehrsanforderungen an *www.contoso.com* werden jetzt an *contoso.trafficmanager.net* umgeleitet.

>[AZURE.IMPORTANT] Sie können nicht mit Domänen der zweiten Ebene wie *contoso.com* auf Traffic Manager-Domänen verweisen. Dies ist eine Einschränkung des DNS-Protokolls, das keine CNAME-Datensätze für Domänennamen der zweiten Ebene zulässt.

## Nächste Schritte

[Traffic Manager-Routingmethoden](traffic-manager-routing-methods.md)

[Deaktivieren, Aktivieren oder Löschen eines Traffic Manager-Profils](disable-enable-or-delete-a-profile.md)

[Deaktivieren oder Aktivieren eines Traffic Manager-Endpunkts](disable-or-enable-an-endpoint.md)

<!---HONumber=AcomDC_0323_2016-->