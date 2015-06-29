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
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# Verwalten von DNS-Einträgen

Diese Anleitung zeigt, wie Sie Datensatzgruppen und Einträge für die DNS-Zone verwalten.

Es ist wichtig, den Unterschied zwischen DNS-Datensatzgruppen und einzelnen DNS-Einträge zu verstehen. Eine Datensatzgruppe ist die Auflistung von Einträgen in einer Zone mit dem gleichen Namen und dem gleichen Typ. Weitere Informationen finden Sie unter [Grundlegendes zu Datensatzgruppen und Einträgen](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Erstellen einer Datensatzgruppe

Datensatzgruppen werden mit dem Cmdlet "New-AzureDnsRecordSet" erstellt. Sie müssen den Namen der Datensatzgruppe, die Zone, die Gültigkeitsdauer (TTL) und den Eintragstyp angeben.

>[AZURE.NOTE]Der Name der Datensatzgruppe muss ein relativer Name ohne Zonenname sein. Der Name der Datensatzgruppe "www" in der Zone "contoso.com" erstellt beispielsweise eine Datensatzgruppe mit dem vollqualifizierten Namen "www.contoso.com".

>Verwenden Sie für einen Datensatz auf oberster Ebene der Zone "@" als Datensatznamen, einschließlich der Anführungszeichen. Der vollqualifizierte Name der Datensatzgruppe entspricht dann dem Zonennamen, in diesem Fall "contoso.com".

Azure DNS unterstützt die folgenden Eintragstypen: A, AAAA, CNAME, MX, NS, SOA, SRV und TXT. Datensatzgruppen vom Typ SOA werden automatisch mit jeder Zone erstellt; sie können nicht separat erstellt werden.

	PS C:> $rs = New-AzureDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Wenn bereits eine Datensatzgruppe vorhanden ist, schlägt der Befehl fehl, es sei denn der Switch "-Overwrite" wird verwendet. Die Option "-Overwrite" löst eine Bestätigungsaufforderung aus, die mit dem Switch "-Force" unterdrückt werden kann.

Im obigen Beispiel wird die Zone mithilfe eines Zonenobjekts angegeben, das von Get-AzureDnsZone oder New-AzureDnsZone zurückgegeben wird. Alternativ können Sie die Zone auch anhand von Zonenname und Ressourcengruppenname angeben:

	PS C:> $rs = New-AzureDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Mit New-AzureDnsRecordSet wird ein lokales Objekt zurückgegeben, das die in Azure DNS erstellte Datensatzgruppe darstellt.

>[AZURE.NOTE]CNAME-Datensatzgruppen können nicht gleichzeitig neben anderen Datensatzgruppen mit dem gleichen Namen vorhanden sein. Sie können z. B. nicht CNAME mit dem relativen Namen "www" und einen A-Eintrag mit dem relativen Namen "www" gleichzeitig erstellen. Da die Zonenspitze (Name = " @") immer die NS- und SOA-Datensatzgruppen enthält, die beim Erstellen der Zone erstellt wurden, bedeutet dies, dass Sie keine CNAME-Datensatzgruppe an der Zonenspitze erstellen können. Diese Einschränkungen ergeben sich aus den DNS-Standards; sie sind keine Einschränkungen von Azure DNS.

## Abrufen einer Datensatzgruppe
Verwenden Sie "Get-AzureDnsRecordSet" zum Abrufen einer vorhandenen Datensatzgruppe; geben Sie dazu den relativen Namen der Datensatzgruppe, den Typ und die Zone an:

	PS C:> $rs = Get-AzureDnsRecordSet -Name www –RecordType A -Zone $zone

Wie bei New-AzureDnsRecordSet muss der Name ein relativer Name sein, d. h., er darf nicht den Zonennamen enthalten. Die Zone kann entweder mithilfe eines Zonenobjekts (wie oben) oder durch den Zonennamen und den Namen der Ressourcengruppe angegeben werden:

	PS C:> $rs = Get-AzureDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Mit Get-AzureDnsRecordSet wird ein lokales Objekt zurückgegeben, das die in Azure DNS erstellte Datensatzgruppe darstellt.

## Auflisten von Datensatzgruppen
Durch Auslassen der Parameter "–Name" und/oder "–RecordType" kann Get-AzureDnsRecordSet auch zum Auflisten von Datensatzgruppen verwendet werden:

### Option 1 
Listen Sie alle Datensatzgruppen auf. Dadurch werden alle Datensatzgruppe zurückgegeben, unabhängig vom Namen oder Eintragstyp:

	PS C:> $list = Get-AzureDnsRecordSet -Zone $zone
### Option 2 

Listen Sie Datensatzgruppen eines bestimmten Eintragstyps auf. Dadurch werden alle Datensatzgruppen zurückgegeben, die dem angegebenen Eintragstyp (in diesem Fall A-Einträge) entsprechen:

	PS C:> $list = Get-AzureDnsRecordSet –RecordType A -Zone $zone 

In beiden Fällen kann die Zone entweder mit einem Zonenobjekt (wie gezeigt) oder durch die Parameter "–ZoneName" und "–ResourceGroupName" angegeben werden.

## Hinzufügen eines Eintrags zu einer Datensatzgruppe
Einträge werden mithilfe des Cmdlets "Add-AzureDnsRecordConfig" den Datensatzgruppen hinzugefügt. Dies ist ein Offline-Vorgang; nur das lokale Objekt, das die Datensatzgruppe darstellt, wird geändert.

Die Parameter zum Hinzufügen von Einträgen zu einer Datensatzgruppe variieren je nach Typ der Datensatzgruppe. Wenn Sie beispielsweise eine Datensatzgruppe vom Typ "A" verwenden, können Sie nur Einträge mit dem Parameter "IPv4Address" angeben.

Weitere Einträge können jeder Datensatzgruppe hinzugefügt werden, indem Add-AzureDnsRecordConfig erneut aufgerufen wird. Sie können einer Datensatzgruppe bis zu 100 Einträge hinzufügen. Datensatzgruppen vom Typ "CNAME" können allerdings höchstens 1 Eintrag enthalten, und eine Datensatzgruppe darf nicht zwei identische Einträge enthalten. Leere Datensatzgruppen (ohne Einträge) können erstellt werden, sie werden jedoch nicht im Azure DNS-Namenserver angezeigt.

Sobald die Datensatzgruppe die gewünschte Auflistung von Einträgen enthält, muss ein Commit mithilfe des Cmdlets " Set-AzureDnsRecordSet" ausgeführt werden, wodurch die vorhandene Datensatzgruppe in Azure DNS durch die bereitgestellte Datensatzgruppe ersetzt wird. Die folgenden Beispiele zeigen, wie Sie eine Datensatzgruppe jedes Eintragstyps mit einem einzelnen Eintrag erstellen.

### Erstellen einer A-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:> $rs = New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

Die Reihenfolge der Vorgänge zum Erstellen einer Datensatzgruppe kann auch "weitergeleitet" werden, wobei das Datensatzgruppenobjekt über die Pipe anstatt als Parameter übergeben wird. Beispiel:

	PS C:> New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet

### Erstellen einer AAAA-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:> $rs = New-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Erstellen einer CNAME-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:> $rs = New-AzureDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Erstellen einer MX-Datensatzgruppe mit einem einzelnen Eintrag
In diesem Beispiel verwenden wir den Namen des Datensatzes "@" zum Erstellen des MX-Eintrags auf oberster Ebene der Zone (z. B. "contoso.com"). Dies ist bei MX-Einträgen üblich.

	PS C:> $rs = New-AzureDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Erstellen einer NS-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:> $rs = New-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs
### Erstellen einer SRV-Datensatzgruppe mit einem einzelnen Eintrag

Wenn Sie einen SRV-Eintrag im Zonenstamm erstellen, geben Sie einfach "_service" und "_protocol" im Eintragsnamen an. Sie müssen nicht zusätzlich ". @" im Eintragsnamen angeben.

	PS C:> $rs = New-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Erstellen einer TXT-Datensatzgruppe mit einem einzelnen Eintrag

	PS C:> $rs = New-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

## Ändern von vorhandenen Datensatzgruppen
Das Ändern von vorhandenen Datensatzgruppen folgt einem ähnlichen Muster wie das Erstellen von Datensatzgruppen. Die Reihenfolge der Vorgänge ist folgendermaßen:

1.	Rufen Sie die vorhandene Datensatzgruppe mit Get-AzureDnsRecordSet ab.
2.	Ändern Sie die Datensatzgruppe durch Hinzufügen von Einträgen, Entfernen von Einträgen, Ändern der Eintragsparameter oder durch Ändern der Gültigkeitsdauer der Datensatzgruppe. Diese Änderungen erfolgen offline; nur das lokale Objekt, das die Datensatzgruppe darstellt, wird geändert.
3.	Führen Sie mithilfe des Cmdlets "Set-AzureDnsRecordSet" ein Commit für Ihre Änderungen aus. Dadurch wird die vorhandene Datensatzgruppe in Azure DNS durch die bereitgestellte Datensatzgruppe ersetzt.

Dies wird in den folgenden Beispielen gezeigt:

### Aktualisieren eines Eintrags in einer vorhandenen Datensatzgruppe
In diesem Beispiel wird die IP-Adresse eines vorhandenen A-Eintrags geändert:

	PS C:> $rs = Get-AzureDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs 

Das Cmdlet "Set-AzureDnsRecordSet" verwendet Etag-Überprüfungen, um sicherzustellen, dass gleichzeitige Änderungen nicht überschrieben werden. Verwenden Sie das Flag "-Overwrite", um diese Überprüfungen zu unterdrücken. Weitere Informationen finden Sie unter "Etags und Tags".

### Ändern des SOA-Eintrags

>[AZURE.NOTE]Sie können der automatisch erstellten SOA-Datensatzgruppe an der Zonenspitze (Name = "@") keine Einträge hinzufügen oder daraus entfernen, Sie können jedoch die Parameter im SOA-Eintrag und die Gültigkeitsdauer der Datensatzgruppe ändern.

Im folgenden Beispiel wird veranschaulicht, wie Sie die Eigenschaft "Email" des SOA-Eintrags ändern:

	PS C:> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:> $rs.Records[0].Email = "admin.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs 

### Ändern der NS-Einträge an der Zonenspitze

>[AZURE.NOTE]Sie können der automatisch erstellten NS-Datensatzgruppe an der Zonenspitze (Name = "@") keine Einträge hinzufügen, daraus entfernen und keine Einträge ändern. Sie können nur die Gültigkeitsdauer (TTL) der Datensatzgruppe ändern.

Im folgenden Beispiel wird veranschaulicht, wie Sie die Eigenschaft "TTL" der NS-Datensatzgruppe ändern:

	PS C:> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:> $rs.Ttl = 300
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs 

### Hinzufügen von Einträgen zu einer vorhandenen Datensatzgruppe
In diesem Beispiel werden einer vorhandenen Datensatzgruppe zwei zusätzliche MX-Einträge hinzugefügt:

	PS C:> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs 

## Entfernen eines Eintrags aus einer vorhandenen Datensatzgruppe

Einträge können mithilfe des Cmdlets "Remove-AzureDnsRecordConfig" aus einer Datensatzgruppe entfernt werden. Beachten Sie, dass der entfernte Eintrag für alle Parameter exakt mit einem vorhandenen Eintrag übereinstimmen muss. Für Änderungen muss mithilfe von Set-AzureDnsRecordSet ein Commit ausgeführt werden.

Durch Entfernen des letzten Eintrags aus einer Datensatzgruppe wird die Datensatzgruppe nicht gelöscht. Weitere Informationen finden Sie nachfolgend unter [Löschen einer Datensatzgruppe](#delete-a-record-set).


	PS C:> $rs = Get-AzureDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

Die Reihenfolge der Vorgänge zum Entfernen einer Datensatzgruppe kann auch "weitergeleitet" werden, wobei das Datensatzgruppenobjekt über die Pipe anstatt als Parameter übergeben wird. Beispiel:

	PS C:> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet
### Entfernen eines AAAA-Eintrags aus einer Datensatzgruppe

	PS C:> $rs = Get-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Entfernen eines CNAME-Eintrags aus einer Datensatzgruppe

Da eine CNAME-Datensatzgruppe höchstens einen Eintrag enthalten kann, bleibt beim Entfernen dieses Eintrags eine leere Datensatzgruppe zurück.

	PS C:> $rs =  Get-AzureDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Entfernen eines MX-Eintrags aus einer Datensatzgruppe

	PS C:> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Entfernen eines NS-Eintrags aus einer Datensatzgruppe
	
	PS C:> $rs = Get-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Entfernen eines SRV-Eintrags aus einer Datensatzgruppe

	PS C:> $rs = Get-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

### Entfernen eines TXT-Eintrags aus einer Datensatzgruppe

	PS C:> $rs = Get-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:> Remove-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:> Set-AzureDnsRecordSet -RecordSet $rs

## Löschen einer Datensatzgruppe
Datensatzgruppen können mit dem Cmdlet "Remove-AzureDnsZone" gelöscht werden.

>[AZURE.NOTE]Sie können nicht die SOA- und NS-Datensatzgruppen an der Zonenspitze (Name = "@") löschen, die beim Erstellen der Zone automatisch erstellt werden. Sie werden automatisch gelöscht, wenn die Zone gelöscht wird.

Verwenden Sie eine der folgenden drei Möglichkeiten, um eine Datensatzgruppe zu löschen:
### Option 1
Geben Sie alle Parameter namentlich an:

	PS C:> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]
Mit dem optionalen Switch "-Force" kann diese Bestätigungsaufforderung unterdrückt werden.

### Option 2
Geben Sie die Datensatzgruppe mit Namen und Typ und die Zone mittels Objekt an:

	PS C:> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Option 3
Geben Sie den Datensatz mittels Objekt an:

	PS C:> Remove-AzureDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Die Angabe der Datensatzgruppe mithilfe eines Objekts ermöglicht die Etag-Überprüfung, um sicherzustellen, dass gleichzeitige Änderungen nicht gelöscht werden. Das optionale Flag "-Overwrite" unterdrückt diese Überprüfungen. Weitere Informationen finden Sie unter [Etags und Tags](../dns-getstarted-create-dnszone#Etags-and-tags).

Das Datensatzgruppenobjekt kann auch weitergeleitet werden, anstatt als Parameter übergeben zu werden:

	PS C:> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordSet [-Overwrite] [-Force]

##Weitere Informationen

[Erste Schritte beim Erstellen von Datensatzgruppen und Einträgen](../dns-getstarted-create-recordset)<BR> [Durchführen von Vorgängen für DNS-Zonen](../dns-operations-dnszones)<BR> [Automatisieren von Vorgängen mit dem .NET SDK](../dns-sdk)
 

<!---HONumber=58_postMigration-->