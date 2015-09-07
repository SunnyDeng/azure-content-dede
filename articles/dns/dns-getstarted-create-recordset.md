<properties
   pageTitle="Erstellen einer Datensatzgruppe und von Einträgen für eine DNS-Zone | Microsoft Azure"
	description="Erstellen von Hosteinträgen für Azure DNS. Einrichten von Datensatzgruppen und Einträgen mithilfe von PowerShell"
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="Adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="joaoma"/>


# Erstellen von DNS-Einträgen


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-recordset-cli.md)
- [Azure Powershell steps](dns-getstarted-create-recordset.md)

Nach dem Erstellen der DNS-Zone müssen Sie die DNS-Einträge für Ihre Domäne hinzufügen. Zu diesem Zweck müssen Sie zunächst Grundlegendes zu DNS-Einträgen und Datensatzgruppen verstehen.


## Grundlegendes zu Datensatzgruppen und Einträgen
Jeder DNS-Eintrag hat einen Namen und einen Typ.

Ein _vollqualifizierter_ Name umfasst den Zonennamen, ein _relativer_ Name hingegen nicht. Der relative Eintragsname "www" in der Zone "contoso.com" gibt beispielsweise den vollqualifizierten Eintragsnamen "www.contoso.com" an.

>[AZURE.NOTE]Einträge in Azure DNS werden mit relativen Namen angegeben.

Es gibt verschiedene Eintragstypen, je nach den darin enthaltenen Daten. Der häufigste Typ ist ein A-Eintrag, der einen Namen einer IPv4-Adresse zuordnet. Ein weiterer Typ ist ein MX-Eintrag, der einen Namen einem Mailserver zuordnet.

Azure DNS unterstützt alle allgemeine DNS-Eintragstypen: A, AAAA, CNAME, MX, NS, SOA, SRV und TXT.

In einigen Fällen müssen Sie mehrere DNS-Einträge mit einem bestimmten Namen und Typ erstellen. Angenommen, die Website "www.contoso.com" wird auf zwei verschiedenen IP-Adressen gehostet. Dazu sind zwei verschiedene A-Einträge erforderlich, einer für jede IP-Adresse:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Dies ist ein Beispiel für eine Datensatzgruppe. Eine Datensatzgruppe ist die Auflistung von DNS-Einträgen in einer Zone mit dem gleichen Namen und dem gleichen Typ. Die meisten Datensatzgruppen enthalten einen einzelnen Eintrag, aber Beispiele wie das obige, in dem eine Datensatzgruppe mehr als einen Eintrag enthält, sind nicht ungewöhnlich. (Datensatzgruppen vom Typ SOA und CNAME stellen eine Ausnahme dar; die DNS-Standards lassen nicht mehrere Einträge mit dem gleichen Namen für diese Typen zu.)

Die Gültigkeitsdauer (TTL oder Time-to-Live) gibt an, wie lange jeder Eintrag von den Clients zwischengespeichert wird, bevor er erneut abgefragt wird. Im obigen Beispiel ist der TTL-Wert 3600 Sekunden oder 1 Stunde. Der TTL-Wert wird für die Datensatzgruppe, nicht für jeden Eintrag angegeben, dadurch wird der gleiche Wert für alle Einträge in dieser Datensatzgruppe verwendet.

>[AZURE.NOTE]Azure DNS verwaltet die DNS-Einträge mithilfe von Datensatzgruppen.



## Erstellen von Datensatzgruppen und Einträgen

Das folgende Beispiel zeigt, wie Sie eine Datensatzgruppe und Einträge erstellen. Wir verwenden den DNS-Datensatztyp "A-Datensatz". Informationen zu anderen Datensatztypen finden Sie unter [Verwalten von DNS-Einträgen](dns-operations-recordsets.md).


### Schritt 1

Erstellen Sie einen Datensatzeintrag, und weisen Sie ihn der Variablen "$rs" zu.

	PS C:\>$rs = New-AzureDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

Die Datensatzgruppe hat den relativen Namen "www" in der DNS-Zone "contoso.com", somit lautet der vollqualifizierte Name der Einträge "www.contoso.com". Der Eintragstyp ist "A", und die Gültigkeitsdauer beträgt 60 Sekunden.

>[AZURE.NOTE]Zum Erstellen einer Datensatzgruppe auf oberster Ebene der Zone (in diesem Fall "contoso.com") verwenden Sie den Namen des Datensatzes "@", einschließlich der Anführungszeichen. Dies ist eine allgemeine DNS-Konvention.

Die Datensatzgruppe ist leer, und wir müssen Einträge hinzufügen, um die neu erstellte Datensatzgruppe "www" verwenden zu können.<BR>

### Schritt 2

Fügen Sie die IPv4-A-Einträge der www-Datensatzgruppe mithilfe der $rs-Variablen hinzu, die beim Erstellen der Datensatzgruppen in Schritt 1 zugewiesen wurden:

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

Das Hinzufügen von Einträgen zu einer Datensatzgruppe mithilfe von AzureDnsRecordConfig ist ein Offline-Vorgang. Nur die lokale Variable "$rs" wird aktualisiert.

### Schritt 3
Übergeben Sie die Änderungen an die Datensatzgruppe. Verwenden Sie Set-AzureDnsRecordSet, um Änderungen an der Datensatzgruppe nach Azure DNS hochzuladen:


	Set-AzureDnsRecordSet -RecordSet $rs

Damit sind die Änderungen abgeschlossen. Sie können die Datensatzgruppe von Azure DNS mithilfe von Get-AzureDnsRecordSet abrufen:


	PS C:\> Get-AzureDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



Sie können auch nslookup oder andere DNS-Tools verwenden, um die neue Datensatzgruppe abzufragen.

>[AZURE.NOTE]Wenn Sie die Domäne noch nicht an die Azure DNS-Namenserver delegiert haben, müssen Sie die Namenserveradresse für die Zone explizit angeben, wie schon beim Erstellen der Zone.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## Nächste Schritte
[Verwalten von DNS-Zonen](dns-operations-dnszones.md)

[Verwalten von DNS-Einträgen](dns-operations-recordsets.md)<BR>

[Automatisieren von Azure-Vorgängen mit dem .NET SDK](dns-sdk.md)
 

<!---HONumber=August15_HO9-->