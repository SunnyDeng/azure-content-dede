
<properties 
    pageTitle="Problembehandlung bei RemoteApp-Cloud-Sammlungen – Erstellen"
    description="Erfahren Sie, wie Sie Probleme beim Erstellen von RemoteApp-Cloud-Sammlungen beheben." 
    services="remoteapp" 
    documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/12/2015" 
    ms.author="vikbucha" />



# Problembehandlung bei der Erstellung von RemoteApp-Cloud-Sammlungen

Folgende Fehler treten im Azure-Verwaltungsportal häufig auf:

	DNS server could not be reached
	ProvisioningTimeout

Cloud-Sammlungen können oftmals nicht erstellt werden, da Sie benutzerdefinierte Images verwenden. Wenn die oben genannten Fehler auftreten und Sie benutzerdefinierte Images für das Erstellen der Sammlung verwenden, prüfen Sie Folgendes:

- Vergewissern Sie sich, dass das von Ihnen hochgeladene benutzerdefinierte Image den Anforderungen entspricht. 
- Am häufigsten tritt das Problem auf, das ein Image nicht richtig mit Sysprep vorbereitet wurde.  
- Überprüfen Sie, ob das Image in Hyper-V gestartet werden kann, oder versuchen Sie, eine IAAS-VM mit diesem Image direkt in Ihrem Azure-Abonnement zu erstellen. Wenn die VM nicht gestartet werden kann, deutet dies meist darauf hin, dass das benutzerdefinierte Image nicht richtig vorbereitet wurde. Überprüfen Sie, ob das benutzerdefinierte Image gemäß den Anweisungen in "Erstellen eines benutzerdefinierten Vorlagenimages für RemoteApp" erstellt wurde.

Wenn Sie ein der Microsoft-Images verwenden, die in Ihrem Abonnement enthalten sind, wiederholen Sie das Erstellen der Sammlung. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.

	PlatformImageTrialModeOnly

Wenn dieser Fehler angezeigt wird, deutet dies i. d. R. darauf hin, dass ein Upgrade Ihres Konto auf ein Bezahlkonto durchgeführt wurde, Sie aber versuchen, ein von Microsoft bereitgestelltes Image zu verwenden, das nur während der Testphase des Diensts gültig ist. Versuchen Sie in diesem Fall, Ihre Cloud-Sammlung erneut zu erstellen, und geben Sie dabei das richtige Image an.
 

<!---HONumber=August15_HO7-->