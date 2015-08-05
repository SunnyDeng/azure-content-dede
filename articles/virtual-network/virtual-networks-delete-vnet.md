<properties 
   pageTitle="Löschen eines virtuellen Netzwerks (VNet)"
   description="Hier erfahren Sie, wie Sie ein vorhandenes VNet löschen."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/29/2015"
   ms.author="telmos" />

# Löschen eines virtuellen Netzwerks (VNet)

Wenn Sie ein VNet löschen möchten, können Sie einfach auf **Löschen** klicken. Zuvor müssen Sie jedoch die folgenden Schritte ausführen:

1. **Speichern Sie die Einstellungen (optional)**: Wenn Sie die Einstellungen des virtuellen Netzwerks in einer lokalen Datei speichern möchten, exportieren Sie die Konfigurationsdatei vor dem Löschen des virtuellen Netzwerks. Weitere Informationen finden Sie unter [Exportieren der Einstellungen eines virtuellen Netzwerks in eine Netzwerkkonfigurationsdatei](https://msdn.microsoft.com/library/azure/dn133804.aspx). Das Speichern der Einstellungen bietet Ihnen die Möglichkeit, das VNet später bei Bedarf neu zu erstellen.

1. **Löschen Sie das virtuelle Netzwerkgateway**: Wenn Sie ein Gateway für das VNet konfiguriert haben, müssen Sie es vor dem Löschen des VNet löschen. Wechseln Sie zum Löschen des virtuellen Netzwerkgateways zur Dashboardseite für Ihr virtuelles Netzwerk. Klicken Sie unten auf der Seite auf **Gateway löschen**.
						
	Sie müssen möglicherweise 5 bis 10 Minuten warten, bis das Gateway gelöscht wurde, bevor Sie mit den nächsten Schritten fortfahren.

1. **Löschen Sie Clouddienste, Websites und VMs**: Wenn Sie Objekte in Ihrem VNet bereitgestellt haben, müssen Sie sie vor dem Löschen des VNet löschen.

1. **Löschen Sie Ihr virtuelles Netzwerk**: Klicken Sie rechts neben der Zeile *Name*, um das zu löschende VNet zu markieren, und klicken Sie dann am unteren Rand der Seite auf **Löschen**. Folgen Sie den Bildschirmanweisungen.

1. **Weitere Schritte**: Sie können auch lokale Netzwerkeinstellungen, DNS-Server und die Affinitätsgruppe löschen, nachdem Sie das virtuelle Netzwerk gelöscht haben.
 

<!---HONumber=July15_HO4-->