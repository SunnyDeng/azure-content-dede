<properties 
   pageTitle="Deaktivierungsinformationen für die Gastbetriebssystemfamilie 1 | Azure" 
   description="Bietet Informationen zum Zeitpunkt der Deaktivierung der Azure-Gastbetriebssystemfamilie 1 und dazu, wie Sie ermitteln, ob Sie betroffen sind" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="02/27/2015"
   ms.author="adegeo"/>



# Deaktivierungsinformationen für die Azure-Gastbetriebssystemfamilie 1

Die Deaktivierung der Betriebssystemfamilie 1 wurde erstmals am 1. Juni 2013 angekündigt.

**2. September 2014** Die Azure-Gastbetriebssystemfamilie 1.x (Gast-BS), die auf dem Betriebssystem Windows Server 2008 basiert, wurde offiziell außer Betrieb gestellt. Alle Versuche der Bereitstellung neuer Dienste oder der Aktualisierung vorhandener Dienste mithilfe von Familie 1 führen zu einer Fehlermeldung, die Sie darüber informiert, dass die Gastbetriebssystemfamilie 1 eingestellt wurde. 

**3. November 2014** Die erweiterte Unterstützung für die Gastbetriebssystemfamilie 1 wurde beendet, und die Familie wurde vollständig außer Betrieb genommen. Alle noch in Familie 1 enthaltenen Dienste sind davon betroffen. Wir können diese Dienste jederzeit beenden. Es gibt keine Garantie dafür, dass Ihre Dienste weiterhin ausgeführt werden können, sofern Sie diese nicht selbst manuell aktualisieren.

Wenn Sie weitere Fragen haben, besuchen Sie die [Cloud Services-Foren](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc), oder wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/).




## Sind Sie betroffen?

Ihre Cloud-Dienste sind betroffen, wenn eine der folgenden Bedingungen zutrifft:

1. In der Datei "ServiceConfiguration.cscfg" Ihres Cloud-Diensts ist explizit der Wert "osFamily = "1" angegeben. 
2. In der Datei "ServiceConfiguration.cscfg" Ihres Cloud-Diensts ist kein expliziter Wert für "osFamily" angegeben. In diesem Fall verwendet das System aktuell den Standardwert "1".
3. Im Azure-Verwaltungsportal ist der Wert Ihrer Gastbetriebssystemfamilie als "Windows Server 2008" angegeben. [Hier](https://msdn.microsoft.com/library/azure/gg456325.aspx) erfahren Sie, wie Sie diesen Wert finden.

Um herauszufinden, welcher Ihrer Cloud-Dienste unter welcher Betriebssystemfamilie ausgeführt wird, können Sie das unten stehende Skript in Azure PowerShell ausführen. [Sie müssen jedoch zuerst Azure PowerShell](install-configure-powershell.md) einrichten. Weitere Details zum Skript finden Sie unter [Einstellung der Azure-Gastbetriebssystemfamilie 1: Juni 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx) (in englischer Sprache). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName 
    
    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""} 

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Ihre Cloud-Dienste sind von der Deaktivierung der Betriebssystemfamilie 1 betroffen, wenn die Spalte "osFamily" in der Ausgabe des Skripts leer ist oder den Wert "1" enthält. 

## Empfehlungen, wenn Sie betroffen sind

Wir empfehlen Ihnen, Ihre Cloud-Dienst-Rollen zu einer der unterstützten Gastbetriebssystemfamilien zu migrieren:

**Gastbetriebssystemfamilie 4.x** - Windows Server 2012 R2 *(empfohlen)*

1. Stellen Sie sicher, dass Ihre Anwendung SDK 2.1 oder höher mit .NET-Framework 4.0, 4.5 oder 4.5.1 verwendet.
2. Legen Sie in der Datei "ServiceConfiguration.cscfg" das osFamily-Attribut auf den Wert "4" fest, und stellen Sie Ihren Cloud-Dienst erneut bereit.


**Gastbetriebssystemfamilie 3.x** - Windows Server 2012

1. Stellen Sie sicher, dass Ihre Anwendung SDK 1.8 oder höher mit .NET-Framework 4.0 oder 4.5 verwendet. 
2. Legen Sie in der Datei "ServiceConfiguration.cscfg" das osFamily-Attribut auf den Wert "3" fest, und stellen Sie Ihren Cloud-Dienst erneut bereit.


**Gastbetriebssystemfamilie 2.x** - Windows Server 2008 R2

1. Stellen Sie sicher, dass Ihre Anwendung SDK 1.3 oder höher mit .NET-Framework 3.5 oder 4.0 verwendet. 
2. Legen Sie in der Datei "ServiceConfiguration.cscfg" das osFamily-Attribut auf den Wert "2" fest, und stellen Sie Ihren Cloud-Dienst erneut bereit.


## Erweiterte Unterstützung für Gastbetriebssystemfamilie 1 wurde am 3. November 2014 beendet 
Cloud-Dienste unter Gastbetriebssystemfamilie 1 werden nicht mehr unterstützt. Migrieren Sie schnellst möglich von Familie 1 zu einer neueren Familie, um Dienstunterbrechungen zu vermeiden. 

<!--HONumber=52-->