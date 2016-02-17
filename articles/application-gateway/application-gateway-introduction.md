<properties
   pageTitle="Einführung in Application Gateway | Microsoft Azure"
   description="Diese Seite bietet eine Übersicht über den Application Gateway-Dienst für Lastenausgleich der Ebene 7, einschließlich Gatewaygrößen, HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität und SSL-Auslagerung."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Was ist Application Gateway?


Microsoft Azure Application Gateway bietet eine von Azure verwaltete HTTP-Lastenausgleichslösung, die auf Lastenausgleich der HTTP-Ebene 7 basiert.

Durch Anwendungslastenausgleich können IT-Administratoren und Entwickler Routingregeln für Netzwerkverkehr basierend auf HTTP erstellen. Der Application Gateway-Dienst bietet hohe Verfügbarkeit und gute Kontrolle. Die Vereinbarung zum Servicelevel und die Preise finden Sie auf den Seiten [SLA](https://azure.microsoft.com/support/legal/sla/) und [Preise](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway unterstützt derzeit die Anwendungsbereitstellung der Ebene 7 für Folgendes:

- HTTP-Lastenausgleich
- Cookiebasierte Sitzungsaffinität
- Secure Sockets Layer (SSL)-Auslagerung


## Lastenausgleich der HTTP-Ebene 7

Azure bietet Lastenausgleich der Ebene 4 über Azure Load Balancer, der auf der Transportebene (TCP/UDP) eingesetzt wird und den Lastenausgleich des gesamten eingehenden Netzwerkverkehrs für den Application Gateway-Dienst übernimmt. Das Application Gateway wendet dann Routingregeln auf den HTTP-Datenverkehr an und ermöglicht so Lastenausgleich der Ebene 7 (HTTP). Wenn Sie ein Application Gateway erstellen, wird ein Endpunkt (VIP) zugeordnet und als öffentliche IP-Adresse für eingehenden Netzwerkverkehr verwendet.

Das Anwendungsgateway leitet den HTTP-Datenverkehr auf Grundlage der Konfiguration weiter: virtueller Computer, Clouddienst, Web-App oder eine externe IP-Adresse.

Lastenausgleich der HTTP-Ebene 7 eignet sich für:

- Anwendungen, für die Anforderungen von einer Benutzer-/Clientsitzung die gleiche virtuelle Back-End-Maschine erreichen müssen. Beispiele hierfür wären Einkaufswagen-Apps und Web-E-Mail-Server.
- Anwendungen, die für Webserverfarmen den Mehraufwand für die SSL-Beendigung unterbinden möchten.
- Anwendungen wie Content Delivery Network, für die mehrere HTTP-Anforderungen auf der gleichen lange bestehenden TCP-Verbindung an verschiedene Back-End-Server weitergeleitet bzw. dort ausgeglichen werden.


## Gatewaygrößen und -instanzen

Application Gateway wird derzeit in drei Größen angeboten: klein, mittel und groß. Kleine Instanzen sind für Entwicklungs- und Testszenarien vorgesehen.

Sie können bis zu 50 Application Gateways pro Abonnement erstellen, und jedes Application Gateway kann jeweils bis zu 10 Instanzen aufweisen. Der Application Gateway-Lastenausgleich als ein von Azure verwalteter Dienst ermöglicht die Bereitstellung eines Lastenausgleichs der Ebene 7 hinter dem Azure Load Balancer.

Die folgende Tabelle zeigt einen durchschnittlichen Durchsatz für jede Anwendungsgatewayinstanz:


| Back-End-Seitenantwort | Klein | Mittel | Groß|
|---|---|---|---|
| 6K | 7,5 MBit/s | 13 MBit/s | 50 MBit/s |
|100k | 35 MBit/s | 100 MBit/s| 200 MBit/s |


>[AZURE.NOTE] Hierbei handelt es sich um ungefähre Richtwerte für den Application Gateway-Durchsatz. Der tatsächliche Durchsatz ist abhängig von verschiedenen Umgebungsdetails wie etwa durchschnittliche Seitengröße, Ort der Back-End-Instanzen und Verarbeitungszeit für die Seitenbereitstellung.

## Systemüberwachung

Azure Application Gateway überprüft die Integrität der Back-End-Instanzen automatisch. Weitere Informationen finden Sie unter [Systemüberwachung des Application Gateways – Übersicht](application-gateway-probe-overview.md).

## Konfigurieren und Verwalten

Sie können ein Application Gateway mithilfe von REST-APIs und PowerShell-Cmdlets erstellen und verwalten.


## Nächste Schritte

Nachdem Sie sich mit dem Application Gateway vertraut gemacht haben, können Sie [ein Application Gateway erstellen](application-gateway-create-gateway.md) oder [ein Application Gateway für SSL-Auslagerung erstellen](application-gateway-ssl.md), um einen Lastenausgleich für HTTPS-Verbindungen durchzuführen.

<!---HONumber=AcomDC_0128_2016-->