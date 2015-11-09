
<properties 
   pageTitle="Übersicht über Internetlastenausgleich | Microsoft Azure"
   description="Übersicht über den Lastenausgleich mit Internetzugriff und die zugehörigen Features. Wie funktioniert ein Lastenausgleich für Azure mithilfe von virtuellen Computern und Clouddiensten."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/16/2015"
   ms.author="joaoma" />


# Internetlastenausgleich zwischen mehreren virtuellen Computern oder Diensten

Endpunkte können für die Konfiguration des Azure-Lastenausgleichsmoduls verwendet werden, um eine bestimmte Art von Netzwerkverkehr zwischen mehreren virtuellen Computern oder Diensten zu verteilen. Sie können zum Beispiel die Netzwerklast von Webanfragen auf mehrere Webserver oder Webrollen verteilen.

Das Azure-Lastenausgleichsmodul führt die Zuordnung zwischen öffentlicher IP-Adresse und Portnummer des eingehenden Datenverkehrs zu den privaten IP-Adressen und Portnummern der virtuellen Computer durch und umgekehrt für den Antwortverkehr vom virtuellen Computer.

>[AZURE.NOTE]Das Azure-Lastenausgleichsmodul ermöglicht unter Verwendung der Standardeinstellungen Hashverteilungsnetzwerkverkehr zwischen mehreren Instanzen des virtuellen Computers (weiterführende Informationen zur Hashverteilung finden Sie in [Lastenausgleichsmodul-Features](load-balancer-overview.md#load-balancer-features)). Wenn Sie Sitzungsaffinität benötigen, lesen Sie die Informationen unter [Lastenausgleichsmodul-Verteilungsmodus](load-balancer-distribution-mode.md).

Für einen Clouddienst, der Instanzen von Web- oder Workerrollen enthält, können Sie einen öffentlichen Endpunkt in der Dienstdefinition (.csdef) definieren.
 
Die Datei "servicedefinition.csdef" enthält die Endpunktkonfiguration. Wenn Sie mehrere Rolleninstanzen für eine Web- oder Workerrollenbereitstellung haben, wird der Lastenausgleich für diese eingerichtet. Die Art, Instanzen zu Ihrer Cloudbereitstellung hinzuzufügen, ändert die Anzahl der Instanzen für die Dienst-Konfigurationsdatei (.csfg).

Die folgende Abbildung zeigt einen Endpunkt für verschlüsselten Datenverkehr mit Lastenausgleich, der von drei virtuellen Computern für den öffentlichen und privaten TCP-Port 443 genutzt wird. Diese drei virtuellen Computer bilden einen Satz mit Lastenausgleich.


![Beispiel für öffentlichen Lastenausgleich](./media/load-balancer-internet-overview/IC727496.png))



Wenn Internetclients Webseitenanfragen an die öffentliche IP-Adresse und TCP-Port 443 des Clouddiensts senden, verteilt der Azure-Lastenausgleich diese Anfragen hashbasiert auf die drei virtuellen Computer im Satz mit Lastenausgleich. Weitere Informationen zu Algorithmen von Lastenausgleichsmodulen finden Sie auf der [Übersichtsseite zum Lastenausgleichsmodul](load-balancer-overview.md#load-balancer-features).


## Nächste Schritte

[Interner Lastenausgleich (Übersicht)](load-balancer-internal-overview.md)

[Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen](load-balancer-internet-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)


 

<!---HONumber=Nov15_HO1-->