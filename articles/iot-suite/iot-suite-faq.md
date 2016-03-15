<properties
  pageTitle="Häufig gestellte Fragen zu Azure IoT Suite | Microsoft Azure | Microsoft Azure"
  description="Häufig gestellte Fragen zu IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="03/02/2016"
  ms.author="araguila"/>
   
# Häufig gestellte Fragen zu IoT Suite

### Wie viele Instanzen von DocumentDB kann ich in einem Abonnement bereitstellen?

Fünf. Sie können ein Support-Ticket erstellen, um dieses Limit zu erhöhen. Doch standardmäßig können Sie nur fünf DocumentDB-Instanzen pro Abonnement bereitstellen. Daher können Sie in einem bestimmten Abonnement nur bis zu fünf vorkonfigurierte Remoteüberwachungslösungen bereitstellen.

### Wie viele Bing Maps-APIs im Tarif „Free“ kann ich in einem Abonnement bereitstellen?

Zwei. Sie können nur zwei Bing Maps-APIs im Tarif „Free“ in einem Abonnement erstellen. Die Remoteüberwachungslösung wird standardmäßig mit einer Bing Maps-API im Tarif „Free“ bereitgestellt. Daher können Sie in einem Abonnement ohne Modifikationen nur bis zu zwei vorkonfigurierte Remoteüberwachungslösungen bereitstellen.

### Was ist der Unterschied zwischen dem Löschen einer Ressourcengruppe im Azure-Portal und dem Klicken auf "Löschen" für eine vorkonfigurierte Lösung in "azureiotsuite.com"?

- Wenn Sie in [azureiotsuite.com][lnk-azureiotsuite] eine vorkonfigurierte Lösung löschen, werden alle Ressourcen gelöscht, die bereitgestellt waren, als Sie die vorkonfigurierte Lösung erstellt haben. Wenn Sie dieser Ressourcengruppe weitere Ressourcen hinzugefügt haben, werden diese ebenfalls gelöscht. 

- Wenn Sie die Ressourcengruppe im [Azure-Portal][lnk-azure-portal] löschen, werden nur die Ressourcen in dieser Ressourcengruppe gelöscht. Sie müssen im klassischen [Azure-Portal][lnk-classic-portal] außerdem die Azure Active Directory-Anwendung löschen, die der vorkonfigurierten Lösung zugeordnet ist.

### Wie lösche ich einen AAD-Mandanten?

Siehe den Blogbeitrag von Eric Golpe [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant].


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0309_2016-->