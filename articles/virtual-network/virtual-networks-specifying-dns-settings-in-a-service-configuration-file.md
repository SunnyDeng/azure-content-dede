<properties 
   pageTitle="Angeben von DNS-Einstellungen in einer Dienstkonfigurationsdatei"
   description="Beschreibung"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="joaoma" />

# Angeben von DNS-Einstellungen in einer Dienstkonfigurationsdatei

## DNS-Elemente

Eine Dienstkonfigurationsdatei kann ein DnsServers-Element mit einer Liste von IPv4-Adressen für die DNS-Server (Domain Name System) enthalten, die der Dienst verwendet. Einstellungen in der Dienstkonfigurationsdatei überschreiben die Einstellungen in der Netzwerkkonfigurationsdatei. Weitere Informationen finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration-Element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING]Das **name**-Attribut im **DnsServer**-Element wird nur als Verweisname verwendet. Es stellt nicht den Hostnamen für den DNS-Server dar. Jeder **DnsServer**-Attributwert muss im gesamten Microsoft Azure-Abonnement eindeutig sein.

## Siehe auch

[Azure-Dienstkonfigurationsschema (CSCFG)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Konfigurationsschema für virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](http://go.microsoft.com/fwlink/?LinkId=248094)

[Einstellungen für virtuelle Netzwerke im Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=248092)

<!---HONumber=August15_HO6-->