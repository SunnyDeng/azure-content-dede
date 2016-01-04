<properties 
   pageTitle="Verwalten von DNS-Datensatzgruppen und Einträgen in Azure DNS | Microsoft Azure" 
   description="Verwalten von DNS-Datensatzgruppen und Einträgen in Azure DNS, wenn Sie Ihre Domäne in Azure DNS hosten. Alle PowerShell-Befehle für Vorgänge für Datensatzgruppen und Einträge." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Verwalten von DNS-Einträgen mithilfe der PowerShell


> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Diese Anleitung zeigt, wie Sie Datensatzgruppen und Einträge für die DNS-Zone verwalten.

Es ist wichtig, den Unterschied zwischen DNS-Datensatzgruppen und einzelnen DNS-Einträge zu verstehen. Eine Datensatzgruppe ist die Auflistung von Einträgen in einer Zone mit dem gleichen Namen und dem gleichen Typ. Weitere Informationen finden Sie unter [Grundlegendes zu Datensatzgruppen und Einträgen](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Erstellen einer Datensatzgruppe

Datensatzgruppen werden mit dem Cmdlet „New-AzureRmDnsRecordSet“ erstellt. Sie müssen den Namen der Datensatzgruppe, die Zone, die Gültigkeitsdauer (TTL) und den Eintragstyp angeben.

>[AZURE.NOTE]Der Name der Datensatzgruppe muss ein relativer Name ohne Zonenname sein. Der Name der Datensatzgruppe "www" in der Zone "contoso.com" erstellt beispielsweise eine Datensatzgruppe mit dem vollqualifizierten Namen "www.contoso.com".

>Verwenden Sie für einen Datensatz auf oberster Ebene der Zone "@" als Datensatznamen, einschließlich der Anführungszeichen. Der vollqualifizierte Name der Datensatzgruppe entspricht dann dem Zonennamen, in diesem Fall "contoso.com".

Azure DNS unterstützt die folgenden Eintragstypen: A, AAAA, CNAME, MX, NS, SOA, SRV und TXT. Datensatzgruppen vom Typ SOA werden automatisch mit jeder Zone erstellt; sie können nicht separat erstellt werden.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Wenn bereits eine Datensatzgruppe vorhanden ist, schlägt der Befehl fehl, es sei denn der Switch "-Overwrite" wird verwendet. Die Option "-Overwrite" löst eine Bestätigungsaufforderung aus, die mit dem Switch "-Force" unterdrückt werden kann.

Im obigen Beispiel wird die Zone mithilfe eines Zonenobjekts angegeben, das von „Get-AzureRmDnsZone“ oder „New-AzureRmDnsZone“ zurückgegeben wird. Alternativ können Sie die Zone auch anhand von Zonenname und Ressourcengruppenname angeben:

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Mit „New-AzureRmDnsRecordSet“ wird ein lokales Objekt zurückgegeben, das die in Azure DNS erstellte Datensatzgruppe darstellt.

>[AZURE.NOTE]CNAME-Datensatzgruppen können nicht gleichzeitig neben anderen Datensatzgruppen mit dem gleichen Namen vorhanden sein. Sie können z. B. nicht CNAME mit dem relativen Namen "www" und einen A-Eintrag mit dem relativen Namen "www" gleichzeitig erstellen. Da die Zonenspitze (Name = " @") immer die NS- und SOA-Datensatzgruppen enthält, die beim Erstellen der Zone erstellt wurden, bedeutet dies, dass Sie keine CNAME-Datensatzgruppe an der Zonenspitze erstellen können. Diese Einschränkungen ergeben sich aus den DNS-Standards; sie sind keine Einschränkungen von Azure DNS.

### Platzhalterdatensätze

Azure DNS unterstützt [Platzhalterdatensätze](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Diese werden für alle Abfragen mit einem übereinstimmenden Namen zurückgegeben (es sei denn, es gibt eine genauere Übereinstimmung aus einer Datensatzgruppe ohne Platzhalter).

>[AZURE.NOTE]Um eine Datensatzgruppe mit Platzhaltern zu erstellen, verwenden Sie den Namen der Datensatzgruppe „*“ oder einen Namen, dessen Bezeichnung mit „*“ beginnt, z. B. „*.foo“.

>Datensatzgruppen mit Platzhaltern werden für alle Datensatztypen mit Ausnahme von NS und SOA unterstützt.

## Abrufen einer Datensatzgruppe

Verwenden Sie „Get-AzureRmDnsRecordSet“ zum Abrufen einer vorhandenen Datensatzgruppe. Geben Sie dazu den relativen Namen der Datensatzgruppe, den Typ und die Zone an:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

Wie bei „New-AzureRmDnsRecordSet“ muss der Name ein relativer Name sein, der nicht den Zonennamen enthalten darf. Die Zone kann entweder mithilfe eines Zonenobjekts (wie oben) oder durch den Zonennamen und den Namen der Ressourcengruppe angegeben werden:

	PS C:\> $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Mit „Get-AzureRmDnsRecordSet“ wird ein lokales Objekt zurückgegeben, das die in Azure DNS erstellte Datensatzgruppe darstellt.

## Auflisten von Datensatzgruppen
Durch Auslassen der Parameter „–Name“ und/oder „–RecordType“ kann „Get-AzureRmDnsRecordSet“ auch zum Auflisten von Datensatzgruppen verwendet werden:

### Option 1: 

Listen Sie alle Datensatzgruppen auf. Dadurch werden alle Datensatzgruppe zurückgegeben, unabhängig vom Namen oder Eintragstyp:

	PS C:\> $list = Get-AzureRmDnsRecordSet -Zone $zone

### Option 2 

Listen Sie Datensatzgruppen eines bestimmten Eintragstyps auf. Dadurch werden alle Datensatzgruppen zurückgegeben, die dem angegebenen Eintragstyp (in diesem Fall A-Einträge) entsprechen:

	PS C:\> $list = Get-AzureRmDnsRecordSet –RecordType A -Zone $zone 

In beiden Fällen kann die Zone entweder mit einem Zonenobjekt (wie gezeigt) oder durch die Parameter "–ZoneName" und "–ResourceGroupName" angegeben werden.

## Hinzufügen eines Eintrags zu einer Datensatzgruppe

Einträge werden mithilfe des Cmdlets „Add-AzureRmDnsRecordConfig“ den Datensatzgruppen hinzugefügt. Dies ist ein Offline-Vorgang; nur das lokale Objekt, das die Datensatzgruppe darstellt, wird geändert.

Die Parameter zum Hinzufügen von Einträgen zu einer Datensatzgruppe variieren je nach Typ der Datensatzgruppe. Wenn Sie beispielsweise eine Datensatzgruppe vom Typ "A" verwenden, können Sie nur Einträge mit dem Parameter "IPv4Address" angeben.

Weitere Einträge können jeder Datensatzgruppe hinzugefügt werden, indem „Add-AzureRmDnsRecordConfig“ erneut aufgerufen wird. Sie können einer Datensatzgruppe bis zu 100 Einträge hinzufügen. Datensatzgruppen vom Typ "CNAME" können allerdings höchstens 1 Eintrag enthalten, und eine Datensatzgruppe darf nicht zwei identische Einträge enthalten. Leere Datensatzgruppen (ohne Einträge) können erstellt werden, sie werden jedoch nicht im Azure DNS-Namenserver angezeigt.

Sobald die Datensatzgruppe die gewünschte Auflistung von Einträgen enthält, muss ein Commit mithilfe des Cmdlets „Set-AzureRmDnsRecordSet“ ausgeführt werden, wodurch die vorhandene Datensatzgruppe in Azure DNS durch die bereitgestellte Datensatzgruppe ersetzt wird. Die folgenden Beispiele zeigen, wie Sie eine Datensatzgruppe jedes Eintragstyps mit einem einzelnen Eintrag erstellen.

### Erstellen einer A-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

Die Reihenfolge der Vorgänge zum Erstellen einer Datensatzgruppe kann auch "weitergeleitet" werden, wobei das Datensatzgruppenobjekt über die Pipe anstatt als Parameter übergeben wird. Beispiel:

	PS C:\> New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Erstellen einer AAAA-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Erstellen einer CNAME-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Erstellen einer MX-Datensatzgruppe mit einem einzelnen Eintrag

In diesem Beispiel verwenden wir den Namen des Datensatzes "@" zum Erstellen des MX-Eintrags auf oberster Ebene der Zone (z. B. "contoso.com"). Dies ist bei MX-Einträgen üblich.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Erstellen einer NS-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Erstellen einer SRV-Datensatzgruppe mit einem einzelnen Eintrag

Wenn Sie einen SRV-Eintrag im Zonenstamm erstellen, geben Sie einfach "\_service" und "\_protocol" im Eintragsnamen an. Sie müssen nicht zusätzlich ". @" im Eintragsnamen angeben.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Erstellen einer TXT-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Ändern von vorhandenen Datensatzgruppen

Das Ändern von vorhandenen Datensatzgruppen folgt einem ähnlichen Muster wie das Erstellen von Datensatzgruppen. Die Reihenfolge der Vorgänge ist folgendermaßen:

1.	Rufen Sie die vorhandene Datensatzgruppe mit „Get-AzureRmDnsRecordSet“ ab.
2.	Ändern Sie die Datensatzgruppe durch Hinzufügen von Einträgen, Entfernen von Einträgen, Ändern der Eintragsparameter oder durch Ändern der Gültigkeitsdauer der Datensatzgruppe. Diese Änderungen erfolgen offline; nur das lokale Objekt, das die Datensatzgruppe darstellt, wird geändert.
3.	Führen Sie mithilfe des Cmdlets „Set-AzureRmDnsRecordSet“ ein Commit für Ihre Änderungen aus. Dadurch wird die vorhandene Datensatzgruppe in Azure DNS durch die bereitgestellte Datensatzgruppe ersetzt.

Dies wird in den folgenden Beispielen gezeigt:

### Aktualisieren eines Eintrags in einer vorhandenen Datensatzgruppe

In diesem Beispiel wird die IP-Adresse eines vorhandenen A-Eintrags geändert:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

Das Cmdlet „Set-AzureRmDnsRecordSet“ verwendet „Etag“-Überprüfungen, um sicherzustellen, dass gleichzeitige Änderungen nicht überschrieben werden. Verwenden Sie das Flag "-Overwrite", um diese Überprüfungen zu unterdrücken. Weitere Informationen finden Sie unter "Etags und Tags".

### Ändern des SOA-Eintrags

>[AZURE.NOTE]Sie können der automatisch erstellten SOA-Datensatzgruppe an der Zonenspitze (Name = "@") keine Einträge hinzufügen oder daraus entfernen, Sie können jedoch die Parameter im SOA-Eintrag und die Gültigkeitsdauer der Datensatzgruppe ändern.

Im folgenden Beispiel wird veranschaulicht, wie Sie die Eigenschaft "Email" des SOA-Eintrags ändern:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Ändern der NS-Einträge an der Zonenspitze

>[AZURE.NOTE]Sie können der automatisch erstellten NS-Datensatzgruppe an der Zonenspitze (Name = "@") keine Einträge hinzufügen, daraus entfernen und keine Einträge ändern. Sie können nur die Gültigkeitsdauer (TTL) der Datensatzgruppe ändern.

Im folgenden Beispiel wird veranschaulicht, wie Sie die Eigenschaft "TTL" der NS-Datensatzgruppe ändern:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Hinzufügen von Einträgen zu einer vorhandenen Datensatzgruppe

In diesem Beispiel werden einer vorhandenen Datensatzgruppe zwei zusätzliche MX-Einträge hinzugefügt:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

## Entfernen eines Eintrags aus einer vorhandenen Datensatzgruppe

Einträge können mithilfe des Cmdlets „Remove-AzureRmDnsRecordConfig“ aus einer Datensatzgruppe entfernt werden. Beachten Sie, dass der entfernte Eintrag für alle Parameter exakt mit einem vorhandenen Eintrag übereinstimmen muss. Für Änderungen muss mithilfe von „Set-AzureRmDnsRecordSet“ ein Commit ausgeführt werden.

Durch Entfernen des letzten Eintrags aus einer Datensatzgruppe wird die Datensatzgruppe nicht gelöscht. Weitere Informationen finden Sie nachfolgend unter [Löschen einer Datensatzgruppe](#delete-a-record-set).


	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

Die Reihenfolge der Vorgänge zum Entfernen einer Datensatzgruppe kann auch "weitergeleitet" werden, wobei das Datensatzgruppenobjekt über die Pipe anstatt als Parameter übergeben wird. Beispiel:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Entfernen eines AAAA-Eintrags aus einer Datensatzgruppe

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Entfernen eines CNAME-Eintrags aus einer Datensatzgruppe

Da eine CNAME-Datensatzgruppe höchstens einen Eintrag enthalten kann, bleibt beim Entfernen dieses Eintrags eine leere Datensatzgruppe zurück.

	PS C:\> $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Entfernen eines MX-Eintrags aus einer Datensatzgruppe

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Entfernen eines NS-Eintrags aus einer Datensatzgruppe
	
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Entfernen eines SRV-Eintrags aus einer Datensatzgruppe

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Entfernen eines TXT-Eintrags aus einer Datensatzgruppe

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Löschen einer Datensatzgruppe
Datensatzgruppen können mit dem Cmdlet „Remove-AzureRmDnsZone“ gelöscht werden.

>[AZURE.NOTE]Sie können nicht die SOA- und NS-Datensatzgruppen an der Zonenspitze (Name = "@") löschen, die beim Erstellen der Zone automatisch erstellt werden. Sie werden automatisch gelöscht, wenn die Zone gelöscht wird.

Verwenden Sie eine der folgenden drei Möglichkeiten, um eine Datensatzgruppe zu löschen:

### Option 1

Geben Sie alle Parameter namentlich an:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]

Mit dem optionalen Switch "-Force" kann diese Bestätigungsaufforderung unterdrückt werden.

### Option 2

Geben Sie die Datensatzgruppe mit Namen und Typ und die Zone mittels Objekt an:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Option 3

Geben Sie den Datensatz mittels Objekt an:

	PS C:\> Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Die Angabe der Datensatzgruppe mithilfe eines Objekts ermöglicht die Etag-Überprüfung, um sicherzustellen, dass gleichzeitige Änderungen nicht gelöscht werden. Das optionale Flag "-Overwrite" unterdrückt diese Überprüfungen. Weitere Informationen finden Sie unter [Etags und Tags](../dns-getstarted-create-dnszone#Etags-and-tags).

Das Datensatzgruppenobjekt kann auch weitergeleitet werden, anstatt als Parameter übergeben zu werden:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

##Weitere Informationen

[Erste Schritte beim Erstellen von Datensatzgruppen und Einträgen](dns-getstarted-create-recordset.md)<BR> [Verwalten von DNS-Zonen](dns-operations-dnszones.md)<BR> [Automatisieren von Vorgängen mit dem .NET SDK](dns-sdk.md)
 

<!---HONumber=AcomDC_1125_2015-->