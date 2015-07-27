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
   ms.date="06/24/2015"
   ms.author="joaoma"/>

# Technische Übersicht über Application Gateway 


Microsoft Azure Application Gateway ist ein von Azure verwalteter Dienst, der dem Azure-VPN-Gateway ähnelt. Application Gateway bietet eine von Azure verwaltete HTTP-Lastenausgleichslösung, die auf dem Routing von Anwendungsanforderungen (Application Request Routing, ARR) von IIS basiert. Der Anwendungsgatewaydienst bietet hohe Verfügbarkeit und gute Kontrolle. Die Vereinbarung zum Servicelevel und Preise finden Sie auf den Seiten [SLA](http://azure.microsoft.com/support/legal/sla/) und [Preise](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway unterstützt derzeit die Anwendungsbereitstellung der Ebene 7 für Folgendes:

- HTTP-Lastenausgleich
- Cookiebasierte Sitzungsaffinität
- SSL-Auslagerung

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## Lastenausgleich der HTTP-Ebene 7
Azure bietet Lastenausgleich der Ebene 4 über einen Softwarelastenausgleich. Dies geschieht implizit für jeden Clouddienst, der eine Lastenausgleichs-VIP (öffentlich oder intern) aufweist. Es gibt jedoch viele Anwendungen, die auf Ebene 7 (HTTP) basierenden Lastenausgleich verwenden können.


Lastenausgleich der HTTP-Ebene 7 eignet sich für:


- Anwendungen, für die Anforderungen von einer Benutzer-/Clientsitzung den gleichen virtuellen Back-End-Computer erreichen müssen. Beispiele hierfür wären Einkaufswagen-Apps und Web-E-Mail-Server.
- Anwendungen, die für Webserverfarmen den Mehraufwand für die SSL-Beendigung unterbinden möchten.
- Anwendungen wie CDN, für die mehrere HTTP-Anforderungen auf der gleichen lange bestehenden TCP-Verbindung an verschiedene Back-End-Server weitergeleitet bzw. dort ausgeglichen werden.

## Gatewaygrößen und -instanzen

Application Gateway wird derzeit in drei Größen angeboten: klein, mittel und groß. Kleine Instanzen sind für Entwicklungs- und Testszenarien vorgesehen.

Sie können bis zu 10 Anwendungsgateways pro Abonnement erstellen und jedes Anwendungsgateway kann jeweils bis zu 10 Instanzen aufweisen. Der Application Gateway-Lastenausgleich als ein von Azure verwalteter Dienst ermöglicht die Bereitstellung eines Lastenausgleichs der Ebene 7 hinter dem Azure-Softwarelastenausgleich.

## Konfigurieren und Verwalten

Sie können das Anwendungsgateway mithilfe von REST-APIs und PowerShell-Cmdlets erstellen und verwalten.

## Nächste Schritte

Erstellen Sie ein Anwendungsgateway. Weitere Informationen finden Sie unter [Erstellen eines Anwendungsgateways](application-gateway-create-gateway.md).

Konfigurieren Sie die SSL-Auslagerung. Weitere Informationen finden Sie unter [Konfigurieren der SSL-Auslagerung mit Application Gateway](application-gateway-ssl.md).

<!---HONumber=July15_HO3-->