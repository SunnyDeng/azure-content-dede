<properties 
   pageTitle="Mit der Azure-Befehlszeilenschnittstelle im Ressourcen-Manager Ihre Reverse-DNS-Einträge für Ihre Dienste verwalten | Microsoft Azure"
   description="Mit der Azure-Befehlszeilenschnittstelle im Ressourcen-Manager Ihre Reverse-DNS-Einträge oder PTR-Einträge für Ihre Dienste verwalten"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Mit der Azure-Befehlszeilenschnittstelle Ihre Reverse-DNS-Einträge für Ihre Dienste verwalten

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md)

## Überprüfung der Reverse-DNS-Einträge 
Um sicherzustellen, dass kein Dritter Reverse-DNS-Einträge erstellen kann, die Ihren Domänen zugeordnet sind, erlaubt Azure die Erstellung von Reverse-DNS-Einträgen nur, wenn Folgendes zutrifft:

- Der ReverseFqdn ist der gleiche wie der vollqualifizierte Domänenname (Fqdn) der öffentlichen IP-Adressressource, für den er angegeben wurde. Oder er entspricht dem „Fqdn“ jeder öffentlichen IP-Adresse innerhalb desselben Abonnements, lautet also beispielsweise „contosoapp1.northus.cloudapp.azure.com.“.

- Der ReverseFqdn löst sich vorwärts in den Namen oder die IP-Adresse derjenigen öffentlichen IP-Adresse auf, für die er angegeben wurde, oder in eine beliebige öffentliche IP-Adresse „Fqdn“, oder in eine IP innerhalb des gleichen Abonnements. Beispielsweise, wenn ReverseFqdn dem Namen „app1.contoso.com.“ entspricht, was ein CName alias für „contosoapp1.northus.cloudapp.azure.com“ ist.

Überprüfungen werden nur durchgeführt, wenn die Reverse-DNS-Eigenschaft für eine öffentliche IP-Adresse eingerichtet oder geändert wird. Es finden keine regelmäßigen Neuüberprüfungen statt.

## Hinzufügen von Reverse-DNS zu vorhandenen öffentlichen IP-Adressen
Sie können mit „azure network public-ip set“ einer vorhandenen öffentlichen IP-Adresse Reverse-DNS hinzufügen:

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Wenn Sie Reverse-DNS einer vorhandenen öffentlichen IP-Adresse hinzufügen möchten, die noch keinen DNS-Namen besitzt, müssen Sie auch einen DNS-Namen angeben. Hierzu können Sie „azure network public-ip set“ verwenden:

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## Erstellen einer öffentlichen IP-Adresse mit Reverse-DNS
Sie können eine neue öffentliche IP-Adresse mit der angegebenen Reverse-DNS-Eigenschaft hinzufügen, indem Sie „azure network public-ip create“ ausführen:

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
 
## Anzeigen von Reverse-DNS für vorhandene öffentliche IP-Adressen
Mithilfe von „azure network public-ip show“ können Sie den konfigurierten Wert für eine vorhandene öffentliche IP-Adresse anzeigen:

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## Entfernen Sie eine Reverse-DNS aus vorhandenen öffentlichen IP-Adressen.
Mithilfe von „azure network public-ip set“ können Sie eine Reverse-DNS-Eigenschaft aus einer öffentlichen IP-Adresse entfernen. Dies erfolgt, indem der ReverseFqdn-Eigenschaftswert auf „leer“ gesetzt wird:

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

[AZURE.INCLUDE [HÄUFIG GESTELLTE FRAGEN](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!----HONumber=AcomDC_0316_2016-->