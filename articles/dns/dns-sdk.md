<properties 
   pageTitle="Automatisieren von DNS- und Datensatzgruppenvorgängen mit dem .NET SDK | Microsoft Azure" 
   description="Automatisieren aller DNS-Vorgänge für Azure DNS mithilfe des .NET SDK." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/20/2015"
   ms.author="joaoma"/>
# Erstellen von DNS-Zonen und -Datensatzgruppen mithilfe des .NET SDK
Sie können die Vorgänge zum Erstellen, Löschen oder Aktualisieren von DNS-Zonen, Datensatzgruppen und Einträgen mithilfe des DNS SDK mit .NET DNS Management-Bibliothek automatisieren. Ein vollständiges Visual Studio-Projekt steht Ihnen [hier](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip) zur Verfügung.

## NuGet-Pakete und Namespace-Deklarationen
Um den DNS-Client zu verwenden, müssen Sie das NuGet-Paket "Azure DNS Management Library" installieren und die DNS-Management-Namespaces Ihrem Projekt hinzufügen. Wechseln Sie zu Visual Studio, öffnen Sie ein Projekt oder ein neues Projekt, und klicken Sie auf "Extras" und die NuGet-Paket-Manager-Konsole. Herunterladen der Azure DNS-Management-Bibliothek:

	using Microsoft.Azure;
	using Microsoft.Azure.Management.Dns;
	using Microsoft.Azure.Management.Dns.Models;

## Initialisieren des DNS-Management-Clients

Der DnsManagementClient enthält die Methoden und Eigenschaften, die für die Verwaltung von DNS-Zonen und Datensatzgruppen erforderlich sind. Damit der Client auf das Abonnement zugreifen kann, müssen die richtigen Berechtigungen eingerichtet und ein AWT-Token generiert werden. Weitere Informationen finden Sie unter "Authentifizieren von Anforderungen des Azure-Ressourcen-Managers".

	// get a token for the AAD application (see linked article for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Erstellen oder Aktualisieren einer DNS-Zone

Zum Erstellen einer DNS-Zone wird ein Zonenobjekt erstellt und an dnsClient.Zones.CreateOrUpdate übergeben. Da DNS-Zonen nicht mit einer bestimmten Region verknüpft sind, wird der Speicherort auf "global" festgelegt.<BR>

Erstellen einer DNS-Zone:

	// create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);


Azure DNS unterstützt eine optimistischen Parallelität namens [Etags](../dns-getstarted-create-dnszone#Etags-and-tags). "Etag" ist eine Eigenschaft der Zone, und "IfNoneMatch" ist eine Eigenschaft in ZoneCreateOrUpdateParameters.

## Erstellen oder Aktualisieren von DNS-Einträgen
DNS-Einträge werden als Datensatzgruppe verwaltet. Eine Datensatzgruppe ist eine Gruppe von Einträgen mit demselben Namen und Eintragstyp in einer Zone. Zum Erstellen oder Aktualisieren einer Datensatzgruppe wird ein RecordSet-Objekt erstellt und an dnsClient.RecordSets.CreateOrUpdate übergeben. Beachten Sie, dass der Name der Datensatzgruppe relativ zum Zonennamen ist, und nicht der vollqualifizierte DNS-Namen. Wieder wird der Standort auf "global" festgelegt.
    
Erstellen von Datensatzgruppen

	// make some records sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
Azure DNS unterstützt die optimistische Parallelität [Etags](../dns-getstarted-create-dnszone#Etags-and-tags). "Etag" ist eine Eigenschaft von RecordSet, und "IfNoneMatch" ist eine Eigenschaft in RecordSetCreateOrUpdateParameters.

## Abrufen von Zonen und Datensatzgruppen
Die Zones- und RecordSets-Auflistungen ermöglichen das Abrufen von Zonen bzw. Datensatzgruppen. Datensatzgruppen werden anhand von Typ, Name und der Zone (und Ressourcengruppe), in der sie sich befinden, identifiziert. Zonen werden anhand von Name und der Ressourcengruppe, in der sie sich befinden, identifiziert.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

##Auflisten von Zonen und Datensatzgruppen

Verwenden Sie die List-Methode in der Zones-Auflistung, um Zonen aufzulisten Verwenden Sie zum Auflisten von Datensatzgruppen List- oder ListAll-Methoden in der RecordSets-Auflistung. Die List-Methode unterscheidet sich von der ListAll-Methode darin, dass nur die Datensatzgruppen des angegebenen Typs zurückgegeben werden.

Das folgende Beispiel zeigt, wie Sie eine Liste der DNS-Zonen und Datensatzgruppen abrufen können:


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}
## Siehe auch 
[Traffic Manager – Übersicht](../traffic-manager-overview)

[Azure DNS – Übersicht](../dns-overview)

[Visual Studio SDK-Beispielprojekt](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=August15_HO6-->