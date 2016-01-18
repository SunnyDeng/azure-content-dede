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
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# Was ist Application Gateway?


Microsoft Azure Application Gateway bietet eine von Azure verwaltete HTTP-Lastenausgleichslösung, die auf Lastenausgleich der HTTP-Ebene 7 basiert.

Durch Anwendungslastenausgleich können IT-Administratoren und Entwickler Routingregeln für Netzwerkverkehr basierend auf HTTP erstellen. Der Application Gateway-Dienst bietet hohe Verfügbarkeit und gute Kontrolle. Die Vereinbarung zum Servicelevel und die Preise finden Sie auf den Seiten [SLA](http://azure.microsoft.com/support/legal/sla/) und [Preise](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway unterstützt derzeit die Anwendungsbereitstellung der Ebene 7 für Folgendes:

- HTTP-Lastenausgleich
- Cookiebasierte Sitzungsaffinität
- SSL-Auslagerung

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

Lastenausgleich der HTTP-Ebene 7 eignet sich für:

- Anwendungen, für die Anforderungen von einer Benutzer-/Clientsitzung den gleichen virtuellen Back-End-Computer erreichen müssen. Beispiele hierfür wären Einkaufswagen-Apps und Web-E-Mail-Server.
- Anwendungen, die für Webserverfarmen den Mehraufwand für die SSL-Beendigung unterbinden möchten.
- Anwendungen wie CDN, für die mehrere HTTP-Anforderungen auf der gleichen lange bestehenden TCP-Verbindung an verschiedene Back-End-Server weitergeleitet bzw. dort ausgeglichen werden.


## Gatewaygrößen und -instanzen

Application Gateway wird derzeit in drei Größen angeboten: klein, mittel und groß. Kleine Instanzen sind für Entwicklungs- und Testszenarien vorgesehen.

Sie können bis zu 50 Application Gateways pro Abonnement erstellen, und jedes Application Gateway kann jeweils bis zu 10 Instanzen aufweisen. Der Application Gateway-Lastenausgleich als ein von Azure verwalteter Dienst ermöglicht die Bereitstellung eines Lastenausgleichs der Ebene 7 hinter dem Azure Load Balancer.

Die folgende Tabelle zeigt einen durchschnittlichen Durchsatz für jede Anwendungsgatewayinstanz:


| Back-End-Seitenantwort | Klein | Mittel | Groß|
|---|---|---|---|
| 6K | 7,5 MBit/s | 13 MBit/s | 50 MBit/s |
|100k | 35 MBit/s | 100 Mbit/s| 200 MBit/s |


>[AZURE.NOTE]Hierbei handelt es sich um ungefähre Richtwerte für den Application Gateway-Durchsatz. Der tatsächliche Durchsatz ist abhängig von verschiedenen Umgebungsdetails wie etwa durchschnittliche Seitengröße, Ort der Back-End-Instanzen und Verarbeitungszeit für die Seitenbereitstellung.

## Systemüberwachung
 

Azure Application Gateway überprüft die Integrität der Back-End-Instanzen automatisch. Weitere Informationen finden Sie unter [Tests und Systemüberwachung bei Application Gateway](application-gateway-probe-overview.md).

## Konfigurieren und Verwalten

Sie können ein Application Gateway mithilfe von REST-APIs und PowerShell-Cmdlets erstellen und verwalten.



## Nächste Schritte

Erstellen Sie ein Anwendungsgateway. Weitere Informationen finden Sie unter [Erstellen eines Application Gateways](application-gateway-create-gateway.md).

Konfigurieren Sie die SSL-Auslagerung. Weitere Informationen finden Sie unter [Konfigurieren der SSL-Auslagerung mit Application Gateway](application-gateway-ssl.md).

<!---HONumber=AcomDC_0107_2016-->