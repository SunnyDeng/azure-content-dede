<properties 
   pageTitle="Einführung in Application Gateway | Microsoft Azure"
   description="Diese Seite bietet eine Übersicht über den Application Gateway-Dienst für Lastenausgleich der Ebene 7, einschließlich Gatewaygrößen, HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität und SSL-Auslagerung."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/23/2015"
   ms.author="joaoma"/>

# Was ist Application Gateway?


Microsoft Azure Application Gateway bietet eine von Azure verwaltete HTTP-Lastenausgleichslösung, die auf Lastenausgleich der HTTP-Ebene 7 basiert.

Durch Anwendungslastenausgleich können IT-Administratoren und Entwickler Routingregeln für Netzwerkverkehr basierend auf HTTP erstellen. Der Application Gateway-Dienst bietet hohe Verfügbarkeit und gute Kontrolle. Die Vereinbarung zum Servicelevel und die Preise finden Sie auf den Seiten [SLA](http://azure.microsoft.com/support/legal/sla/) und [Preise](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway unterstützt derzeit die Anwendungsbereitstellung der Ebene 7 für Folgendes:

- HTTP-Lastenausgleich
- Cookiebasierte Sitzungsaffinität
- SSL-Auslagerung

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## Lastenausgleich der HTTP-Ebene 7

Azure bietet Lastenausgleich der Ebene 4 über Azure Load Balancer, der auf der Transportebene (TCP/UDP) eingesetzt wird und den Lastenausgleich des gesamten eingehenden Netzwerkverkehr für den Application Gateway-Dienst übernimmt. Das Anwendungsgateway wendet dann Routingregeln auf den HTTP-Datenverkehr an und ermöglicht so Lastenausgleich der Ebene 7 (HTTP). Wenn Sie ein Anwendungsgateway erstellen, wird ein Endpunkt (VIP) zugeordnet und als öffentliche IP-Adresse für eingehenden Netzwerkverkehr verwendet.

Das Anwendungsgateway leitet den HTTP-Datenverkehr auf Grundlage der Konfiguration weiter: virtueller Computer, Clouddienst, Web-App oder eine externe IP-Adresse.

Das folgende Diagramm veranschaulicht den Datenfluss für ein Application Gateway:

 
![Application Gateway2](./media/application-gateway-introduction/appgateway2.png)

Lastenausgleich der HTTP-Ebene 7 eignet sich für:


- Anwendungen, für die Anforderungen von einer Benutzer-/Clientsitzung den gleichen virtuellen Back-End-Computer erreichen müssen. Beispiele hierfür wären Einkaufswagen-Apps und Web-E-Mail-Server.
- Anwendungen, die für Webserverfarmen den Mehraufwand für die SSL-Beendigung unterbinden möchten.
- Anwendungen wie CDN, für die mehrere HTTP-Anforderungen auf der gleichen lange bestehenden TCP-Verbindung an verschiedene Back-End-Server weitergeleitet bzw. dort ausgeglichen werden.

## Gatewaygrößen und -instanzen

Application Gateway wird derzeit in drei Größen angeboten: klein, mittel und groß. Kleine Instanzen sind für Entwicklungs- und Testszenarien vorgesehen.

Sie können bis zu 10 Anwendungsgateways pro Abonnement erstellen und jedes Anwendungsgateway kann jeweils bis zu 10 Instanzen aufweisen. Der Application Gateway-Lastenausgleich als ein von Azure verwalteter Dienst ermöglicht die Bereitstellung eines Lastenausgleichs der Ebene 7 hinter dem Azure Load Balancer.

## Konfigurieren und Verwalten

Sie können das Anwendungsgateway mithilfe von REST-APIs und PowerShell-Cmdlets erstellen und verwalten.

## Nächste Schritte

Erstellen Sie ein Anwendungsgateway. Weitere Informationen finden Sie unter [Erstellen eines Application Gateways](application-gateway-create-gateway.md).

Konfigurieren Sie die SSL-Auslagerung. Weitere Informationen finden Sie unter [Konfigurieren der SSL-Auslagerung mit Application Gateway](application-gateway-ssl.md).

<!---HONumber=Sept15_HO4-->