<properties 
   pageTitle="Dynamisches Aktualisieren der ExpressRoute-Bandbreite | Microsoft Azure"
   description="Dynamisches Erhöhen der Bandbreite einer ExpressRoute-Verbindung ohne Ausfallzeiten."
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# Dynamisches Aktualisieren der ExpressRoute-Verbindungsbandbreite ohne Ausfallzeiten

Sie können die Größe einer ExpressRoute-Verbindung ohne Ausfallzeit erhöhen. Diese Anweisungen helfen Ihnen bei der Aktualisierung der Bandbreite einer ExpressRoute-Verbindung mit PowerShell. Weitere Informationen zu Beschränkungen und Grenzwerten finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md).

##  Konfigurationsvoraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen vorhanden sind:

- Ein Azure-Abonnement
- Aktuelle Version von Azure PowerShell
- Eine aktive ExpressRoute-Verbindung, die konfiguriert wurde und in Betrieb ist


##  Konfigurieren von Einstellungen mithilfe von PowerShell

Windows PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen finden Sie in der PowerShell-Dokumentation in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Abrufen der Details der ExpressRoute-Verbindung**

	Die Details Ihrer ExpressRoute-Verbindung können Sie mit dem folgenden PowerShell-Cmdlet abrufen:
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Dieser Befehl gibt eine Liste aller Verbindungen zurück, die Sie innerhalb Ihres Abonnements erstellt haben. Mit dem folgenden Befehl können Sie die Details einer bestimmten ExpressRoute-Verbindung abrufen, sofern Sie den Dienstschlüssel kennen:

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


3. **Erhöhen der Bandbreite der ExpressRoute-Verbindung auf der Microsoft-Seite**
	
	Informationen zu unterstützten Bandbreitenoptionen für Ihren Anbieter finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md). Sie können jede Größe auswählen, die die Größe der vorhandenen Verbindung übersteigt. Sobald Sie sich für die benötigte Größe entschieden haben, können Sie den folgenden Befehl verwenden, um die Größe der Verbindung anzupassen.

		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Die Größe Ihrer Verbindung wurde bereits auf der Microsoft-Seite angepasst. Beachten Sie, dass wir Ihnen ab diesem Zeitpunkt die aktualisierte Bandbreitenoption in Rechnung stellen.

4. **Erhöhen der Bandbreite der ExpressRoute-Verbindung auf der Anbieterseite**

	Wenden Sie sich an Ihren Verbindungsanbieter (NSP/EXP), und stellen Sie Informationen zur aktualisierten Bandbreite bereit. Führen Sie den Aktualisierungsprozess in der von Ihrem Dienstanbieter vorgeschriebenen Reihenfolge durch, um die Aufgabe abzuschließen.

 

<!---HONumber=62-->