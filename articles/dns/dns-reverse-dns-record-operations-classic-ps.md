<properties 
   pageTitle="Mit PowerShell im klassischen Bereitstellungsmodell Ihre Reverse-DNS-Einträge für Ihre Dienste verwalten | Microsoft Azure"
   description="Mit PowerShell im klassischen Bereitstellungsmodell Reverse-DNS-Einträge oder PTR-Einträge für Azure-Dienste verwalten"
   services="DNS"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/22/2016"
   ms.author="joaoma" />

# Mit PowerShell Ihre Reverse-DNS-Einträge für Ihre Dienste (klassisch) verwalten

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-classic-selectors-include.md](../../includes/dns-reverse-dns-record-operations-classic-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](dns-reverse-dns-record-operations-ps.md).

## Fügen Sie einem vorhandenen Clouddienst einen Reverse-DNS-Eintrag hinzu.
Sie können einem vorhandenen Clouddienst einen Reverse-DNS-Eintrag mit dem Cmdlet Set-AzureService hinzufügen.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Erstellen Sie einen neuen Clouddienst mit Reverse-DNS-Eintrag. 
Sie können einen neuen Clouddienst mit der angegebenen Reverse-DNS-Eigenschaft hinzufügen, indem Sie das Cmdlet Set-AzureService verwenden:

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Betrachten Sie einen Reverse-DNS-Eintrag für einen vorhandenen Clouddienst.
Sie können den konfigurierten Wert für einen vorhandenen Clouddienst mithilfe des Cmdlets Get-AzureService anzeigen.

	PS C:\> Get-AzureService "contosoapp1"

## Entfernen Sie einen Reverse-DNS-Eintrag aus einem vorhandenen Clouddienst.
Sie können eine Reverse-DNS-Eingenschaft mit dem Cmdlet Set-AzureService aus einem vorhandenen Clouddienst entfernen. Dies erfolgt, indem der Wert für die Reverse-DNS-Eigenschaft auf „leer“ gesetzt wird.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

## Überprüfung der Reverse-DNS-Einträge
Um sicherzustellen, dass kein Dritter Reverse-DNS-Einträge erstellen kann, die Ihren Domänen zugeordnet sind, erlaubt Azure die Erstellung von Reverse-DNS-Einträgen nur, wenn Folgendes zutrifft:

- Der Reverse-DNS-FQDN ist der Name des Clouddiensts, für den er angegeben wurde, oder ein beliebiger Clouddienstname innerhalb des gleichen Abonnements. Beispielsweise ist das Reverse-DNS der Name von „contosoapp1.cloudapp.net.“
- Der Reverse-DNS-FQDN löst sich vorwärts in den Namen oder die IP des Cloud Services auf, für den er angegeben wurde, oder in jeden anderen Clouddienstnamen oder IP innerhalb des gleichen Abonnements. Beispielsweise ist das Reverse-DNS der Name von „app1.contoso.com.“, ein CName alias für „contosoapp1.cloudapp.net“. Überprüfungen werden nur durchgeführt, wenn die Reverse-DNS-Eigenschaft für einen Clouddienst eingerichtet oder geändert wird. Es finden keine regelmäßigen Neuüberprüfungen statt.

[AZURE.INCLUDE [HÄUFIG GESTELLTE FRAGEN](../../includes/dns-reverse-dns-record-operations-faq-include.md)]

<!---HONumber=AcomDC_0309_2016-->