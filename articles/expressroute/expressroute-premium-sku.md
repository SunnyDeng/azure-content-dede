<properties 
   pageTitle="Aktivieren oder Deaktivieren des ExpressRoute Premium-Add-Ons | Microsoft Azure"
   description="Aktivieren oder Deaktivieren des ExpressRoute Premium-Add-Ons für eine ExpressRoute-Verbindung Mit ExpressRoute Premium können Sie Ihrer ExpressRoute-Verbindung bis zu 10.000 Routen für öffentliches und privates Peering und bis zu 10 virtuelle Netzwerke hinzufügen. Sie können auch ein virtuelles Netzwerk einer Region mit einer ExpressRoute-Verbindung einer anderen Region verknüpfen."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# Konfigurieren des ExpressRoute Premium-Add-Ons für Ihre ExpressRoute-Verbindung

ExpressRoute Premium ist eine Sammlung der nachfolgend aufgeführten Features:

 - Höheres Routingtabellen-Limit von 4000 Routen auf 10.000 Routen für öffentliches und privates Peering.
 - Größere Anzahl an virtuellen Netzwerken (VNets), die mit der ExpressRoute-Verbindung verbunden werden können. (Der Standardwert ist 10.) 
 - Globale Konnektivität über das Microsoft-Kernnetzwerk. Jetzt können Sie ein VNet in einer geopolitischen Region mit einer ExpressRoute-Verbindung in einer anderen Region verknüpfen. **Beispiel:** Sie können ein in Westeuropa erstelltes VNet mit einer ExpressRoute-Verbindung verknüpfen, die im Silicon Valley erstellt wurde.

Weitere Informationen zum ExpressRoute Premium-Add-On finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md). Angaben zu den Preisen finden Sie auf der Seite [Preisübersicht](http://azure.microsoft.com/pricing/details/expressroute/).

Die nachfolgenden Anweisungen helfen Ihnen bei der Ausführung der folgenden Aktionen:

- Erstellen einer ExpressRoute-Verbindung mit aktiviertem Premium-Add-On
- Aktualisieren einer vorhandenen ExpressRoute-Verbindung zur Aktivierung des Premium-Add-Ons
- Deaktivieren des ExpressRoute Premium-Add-Ons für eine Verbindung


## So erstellen Sie eine ExpressRoute-Verbindung mit aktivierten Features des Premium-Add-Ons

###  Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen vorhanden sind:

- Ein Azure-Abonnement
- Aktuelle Version von Azure PowerShell

###  1. Importieren Sie das PowerShell-Modul für ExpressRoute

Windows PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen finden Sie in der PowerShell-Dokumentation in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

Importieren Sie das PowerShell-Modul für ExpressRoute mit den folgenden Cmdlets:


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2. Konfigurieren Sie eine neue ExpressRoute-Verbindung mit aktivierten Features des Premium-Add-Ons

Die Features des Premium-Add-Ons können bei der Erstellung einer neuen ExpressRoute-Verbindung aktiviert werden. Folgen Sie den Anweisungen zum Erstellen von ExpressRoute-Verbindungen mit [NSPs](expressroute-configuring-nsps.md) oder [EXPs](expressroute-configuring-exps.md). Mit dem neuen optionalen Parameter des Cmdlets New-AzureDedicatedCircuit können Sie die SKU angeben. Die SKU kann Standard oder Premium sein. Der Standardwert ist "Standard". Wenn für die SKU "Premium" angegeben ist, wird die Verbindung mit den Features des Premium-Add-Ons aktiviert.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3. Überprüfen Sie, ob das ExpressRoute Premium-Add-on aktiviert ist
Sie können überprüfen, ob das ExpressRoute Premium-Add-On für Ihre Verbindung aktiviert ist. Im folgenden Beispiel ist die ExpressRoute-Verbindung nicht mit den Features des ExpressRoute Premium-Add-Ons aktiviert. Wenn das Add-on aktiviert ist, wird als SKU ***Premium*** angezeigt.

		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## So aktivieren Sie das ExpressRoute Premium-Add-On für eine vorhandene ExpressRoute-Verbindung
Die Features des ExpressRoute Premium-Add-Ons können für jede bereits vorhandene ExpressRoute-Verbindung aktiviert werden.


1. **Abrufen der Details der ExpressRoute-Verbindung**

	Die Details Ihrer ExpressRoute-Verbindung können Sie mit dem folgenden PowerShell-Cmdlet abrufen:
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Dieser Befehl gibt eine Liste aller Verbindungen zurück, die Sie innerhalb Ihres Abonnements erstellt haben. Mit dem folgenden Befehl können Sie die Details einer bestimmten ExpressRoute-Verbindung abrufen, sofern Sie den Dienstschlüssel kennen.

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Ersetzen Sie <skey> durch den korrekten Dienstschlüssel.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **Aktivieren des ExpressRoute Premium-Add-Ons für die Verbindung**


	Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet aktivieren:
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	Für Ihre Verbindung sind nun die Features des ExpressRoute Premium-Add-Ons aktiviert. Beachten Sie, dass die Gebühren für das Premium-Add-On ab der erfolgreichen Ausführung des Befehls erhoben werden.


## So deaktivieren Sie das ExpressRoute Premium-Add-On für eine ExpressRoute-Verbindung

Für eine ExpressRoute-Verbindung mit aktiviertem Premium-Add-On kann das Add-On auch wieder deaktiviert werden.

**Hinweis**: Vor der Deaktivierung des Add-Ons müssen Sie die Features aus der Liste der Premium-Add-On-Features stoppen. Wenn mit Ihrer Verbindung mehr als 10 VNets verknüpft sind, schlägt Ihre Anfrage zur Deaktivierung des Premium-Add-Ons fehl. Auch werden unsere BGP-Sitzungen getrennt, wenn Sie bei deaktiviertem Premium-Add-On mehr als 5000 Routen an uns anbieten.

1. **Abrufen der Details der ExpressRoute-Verbindung**

	Die Details Ihrer ExpressRoute-Verbindung können Sie mit dem folgenden PowerShell-Cmdlet abrufen:
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Dieser Befehl gibt eine Liste aller Verbindungen zurück, die Sie innerhalb Ihres Abonnements erstellt haben. Mit dem folgenden Befehl können Sie die Details einer bestimmten ExpressRoute-Verbindung abrufen, sofern Sie den Dienstschlüssel kennen.

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Ersetzen Sie <skey> durch den korrekten Dienstschlüssel.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **Deaktivieren des ExpressRoute Premium-Add-Ons für die Verbindung**


	Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet deaktivieren:
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	Das ExpressRoute Premium-Add-On ist nun für Ihre Verbindung deaktiviert.


 

<!---HONumber=62-->