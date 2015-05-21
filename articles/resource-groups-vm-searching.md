<properties
   pageTitle="Suche nach"
   description="Artikelbeschreibung, die auf Angebotsseiten und für die meisten Suchergebnisse angezeigt wird."
   services="service-name"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/27/2015"
   ms.author="rasquill"/>

# Navigieren und Aussuchen von Azure VM-Datenträgerabbildern mit PowerShell und der Azure-Befehlszeilenschnittstelle


## Azure-Befehlszeilenschnittstelle

Der schnellste und einfachste Weg ein Datenträgerabbild zu suchen, um es entweder mit `azure vm quick-create` zu verwenden oder um eine Vorlagendatei einer Ressourcengruppe zu erstellen, ist den Befehl `azure vm image list` aufzurufen und den Speicherort, den Herausgebernamen \(keine Unterscheidung zwischen Groß- und Kleinschreibung\) und ein Angebot - wenn Sie es kennen - zu übergeben. Zum Beispiel ist die folgende Liste nur ein kurzes Beispiel \(einige Listen sind sehr lang\), wenn Sie wissen, dass „Canonical“ ein Herausgeber für das „UbuntuServer“-Angebot ist.

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240
    
Die **URN**-Spalte ist das Formular, dass Sie an `azure vm quick-create` weiterleiten.
    
Oftmals ist jedoch noch unbekannt, was verfügbar ist. In diesem Fall können Sie durch Datenträgerabbilder navigieren, indem Sie mithilfe von `azure vm image list-publishers` zuerst Herausgeber entdecken und auf die Speicherort-Eingabeaufforderung mit dem Rechenzentrum-Speicherort antworten, den Sie voraussichtlich für Ihre Ressourcengruppe verwenden werden. Beispielsweise werden im Folgenden alle Herausgeber von Datenträgerabbildern am Speicherort „West US“ aufgelistet \(übergeben Sie das Speicherort-Argument mit Kleinbuchstaben und Entfernen von Leerzeichen von Standardspeicherorten\).

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")                
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  

    
Diese Listen können ziemlich lang sein und das oben dargestellte Beispiel zeigt lediglich einen Ausschnitt. Angenommen, Sie würden bemerken, dass Canonical tatsächlich ein Herausgeber am Speicherort „West US“ ist, dann könnten Sie nun seine Angebote finden, indem Sie „azure vm image list-offers“ aufrufen und den Speicherort sowie Herausgeber über die Eingabeaufforderung übergeben, wie im folgenden Beispiel gezeigt:

    azure vm image list-offers           
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK
    
Jetzt wissen wir, dass in der Region „West US“ Canonical das **UbuntuServer**-Angebot in Azure herausgibt. Aber welche SKUs? Rufen Sie `azure vm image list-skus` ab und antworten Sie auf die Eingabeaufforderung mit dem Standort, Herausgeber und dem von Ihnen entdeckten Angebot, um diese zu erhalten.

    azure vm image list-skus           
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK
    
Mit diesen Informationen können Sie nun genau das von Ihnen gewünschte Datenträgerabbild finden, indem Sie den ursprünglichen Aufruf oben aufrufen.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422 
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK
    
Jetzt können Sie genau das Datenträgerabbild auswählen, das Sie verwenden möchten. Weitere Informationen, um schnell mithilfe der von Ihnen soeben gefundenen URN-Informationen einen virtuellen Computer zu erstellen oder eine Vorlage mit diesen URN-Informationen zu verwenden, finden Sie unter [Verwenden der Azure Befehlszeilenschnittstelle für Mac, Linux und Windows mit Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## PowerShell


Wenn Sie mit Azure Resource Manager einen neuen virtuellen Computer erstellen, kann es sein, dass Sie in einigen Fällen ein Datenträgerabbild mit den folgenden Datenträgerabbild-Eigenschaften angeben müssen:

- Herausgeber
- Angebot
- SKU

Diese Werte werden beispielsweise für das PowerShell-Cmdlet **Set-AzureVMSourceImage** oder mit einer Vorlagendatei einer Ressourcengruppe benötigt, in der Sie den Typ des zu erstellenden virtuellen Computers angeben müssen.

Wenn Sie diese Werte angeben müssen, können Sie durch die Datenträgerabbilder navigieren, um diese Werte folgendermaßen zu bestimmen:

1. Auflistung der Herausgeber von Datenträgerabbildern.
2. Auflistung der Angebote eines bestimmten Anbieters.
3. Auflistung der SKUs eines bestimmten Angebots.

Wechseln Sie zuerst zum Resource Manager-Modus von Azure PowerShell, um dies in PowerShell durchzuführen.

	Switch-AzureMode AzureResourceManager 

Listen Sie für den ersten oben genannten Schritt die Herausgeber mit diesen Befehlen auf.

	$locName="<Azure location, such as US West>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

Tragen Sie den von Ihnen gewählten Herausgebernamen ein und führen Sie diese Befehle aus.

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Tragen Sie den von Ihnen gewählten Angebotsnamen ein und führen Sie diese Befehle aus.

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Sie können der Anzeige des Befehls **Get-AzureVMImageSku** alle Informationen entnehmen, die Sie brauchen, um das Datenträgerabbild für einen neuen virtuellen Computer anzugeben.

Beispiel:

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName
	
	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

Für den Herausgeber „MicrosoftWindowsServer“:

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer
	
	Offer
	-----
	WindowsServer

Für das Angebot „WindowsServer“:

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
	
	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

Wenn Sie den gewählten SKU-Namen aus dieser Liste kopieren, erhalten Sie alle Informationen für das PowerShell-Cmdlet **Set-AzureVMSourceImage** oder für eine Vorlagendatei einer Ressourcengruppe, für die Sie den Herausgeber, das Angebot und SKU für ein Datenträgerabbild angeben müssen.


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!--HONumber=52-->
