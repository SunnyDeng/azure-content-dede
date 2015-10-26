<properties 
   pageTitle="Verwalten von DNS-Datensatzgruppen und Einträgen in Azure DNS mithilfe der Befehlszeilenschnittstelle (CLI) | Microsoft Azure" 
   description="Verwalten von DNS-Datensatzgruppen und Einträgen in Azure DNS, wenn Sie Ihre Domäne in Azure DNS hosten. Alle Befehle der Befehlszeilenschnittstelle (CLI) für Vorgänge für Datensatzgruppen und Einträge." 
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Verwalten von DNS-Einträgen mithilfe der Befehlszeilenschnittstelle (CLI)

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [Azure Powershell](dns-operations-recordsets.md)

Diese Anleitung zeigt, wie Sie Datensatzgruppen und Einträge für die DNS-Zone verwalten.

Es ist wichtig, den Unterschied zwischen DNS-Datensatzgruppen und einzelnen DNS-Einträge zu verstehen. Eine Datensatzgruppe ist die Auflistung von Einträgen in einer Zone mit dem gleichen Namen und dem gleichen Typ. Weitere Informationen finden Sie unter [Grundlegendes zu Datensatzgruppen und Einträgen](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records).

## Erstellen einer Datensatzgruppe

Datensatzgruppen werden mit dem Befehl `azure network dns record-set create` erstellt. Sie müssen den Namen der Datensatzgruppe, die Zone, die Gültigkeitsdauer (TTL) und den Eintragstyp angeben.

>[AZURE.NOTE]Der Name der Datensatzgruppe muss ein relativer Name ohne Zonenname sein. Der Name der Datensatzgruppe "www" in der Zone "contoso.com" erstellt beispielsweise eine Datensatzgruppe mit dem vollqualifizierten Namen "www.contoso.com".

>Verwenden Sie für einen Datensatz auf oberster Ebene der Zone "@" als Datensatznamen, einschließlich der Anführungszeichen. Der vollqualifizierte Name der Datensatzgruppe entspricht dann dem Zonennamen, in diesem Fall "contoso.com".

Azure DNS unterstützt die folgenden Eintragstypen: A, AAAA, CNAME, MX, NS, SOA, SRV und TXT. Datensatzgruppen vom Typ SOA werden automatisch mit jeder Zone erstellt; sie können nicht separat erstellt werden. Beachten Sie, dass [der SPF-Datensatztyp durch die DNS-Standards zugunsten des Erstellens von SPF-Datensätzen mit dem Datensatztyp TXT verworfen wurde](http://tools.ietf.org/html/rfc7208#section-3.1).

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT]CNAME-Datensatzgruppen können nicht gleichzeitig neben anderen Datensatzgruppen mit dem gleichen Namen vorhanden sein. Sie können z. B. nicht CNAME mit dem relativen Namen "www" und einen A-Eintrag mit dem relativen Namen "www" gleichzeitig erstellen. Da die Zonenspitze (Name = " @") immer die NS- und SOA-Datensatzgruppen enthält, die beim Erstellen der Zone erstellt wurden, bedeutet dies, dass Sie keine CNAME-Datensatzgruppe an der Zonenspitze erstellen können. Diese Einschränkungen ergeben sich aus den DNS-Standards; sie sind keine Einschränkungen von Azure DNS.

### Platzhalterdatensätze

Azure DNS unterstützt [Platzhalterdatensätze](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Diese werden für alle Abfragen mit einem übereinstimmenden Namen zurückgegeben (es sei denn, es gibt eine genauere Übereinstimmung aus einer Datensatzgruppe ohne Platzhalter).

>[AZURE.NOTE]Um eine Datensatzgruppe mit Platzhaltern zu erstellen, verwenden Sie den Namen der Datensatzgruppe „*“ oder einen Namen, dessen Bezeichnung mit „*“ beginnt, z. B. „*.foo“.

>Datensatzgruppen mit Platzhaltern werden für alle Datensatztypen mit Ausnahme von NS und SOA unterstützt.

## Abrufen einer Datensatzgruppe
Um eine vorhandene Datensatzgruppe abzurufen, verwenden Sie `azure network dns record-set show`, indem Sie die Ressourcengruppe, den Zonennamen, den relativen Namen der Datensatzgruppe und den Datensatztyp angeben:

	azure network dns record-set show myresourcegroup contoso.com www A


## Auflisten von Datensatzgruppen

Sie können alle Datensätze in einer DNS-Zone mit dem Befehl `azure network dns record-set list` auflisten:

### Option 1: 
Listen Sie alle Datensatzgruppen auf. Dadurch werden alle Datensatzgruppe zurückgegeben, unabhängig vom Namen oder Eintragstyp:

	azure network dns record-set list myresourcegroup contoso.com

### Option 2 

Listen Sie Datensatzgruppen eines bestimmten Eintragstyps auf. Dadurch werden alle Datensatzgruppen zurückgegeben, die dem angegebenen Eintragstyp (in diesem Fall A-Einträge) entsprechen:


	azure network dns record-set list myresourcegroup contoso.com A 

In beiden Fällen geben Sie den Namen der Ressourcengruppe und den der Zone an.

## Hinzufügen eines Eintrags zu einer Datensatzgruppe

Datensätze werden mithilfe von `azure network dns record-set add-record` zu Datensatzgruppen hinzugefügt.

Die Parameter zum Hinzufügen von Einträgen zu einer Datensatzgruppe variieren je nach Typ der Datensatzgruppe. Wenn Sie beispielsweise eine Datensatzgruppe vom Typ "A" verwenden, können Sie nur Einträge mit dem Parameter "-a `<IPv4 address>`" angeben.

Die folgenden Beispiele zeigen, wie Sie eine Datensatzgruppe jedes Eintragstyps mit einem einzelnen Eintrag erstellen.

### Erstellen einer A-Datensatzgruppe mit einem einzelnen Eintrag

Um eine Datensatzgruppe zu erstellen, verwenden Sie `azure network dns record-set create`, indem Sie die Ressourcengruppe, den Zonennamen, den relativen Namen der Datensatzgruppe, den Datensatztyp und die Gültigkeitsdauer (TTL) angeben:
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE]Wenn der "--ttl"-Parameter nicht definiert ist, liegt der Standardwert bei 4 (in Sekunden).


Fügen Sie nach dem Erstellen der A-Datensatzgruppe mit `azure network dns record-set add-record` der Datensatzgruppe die IPv4 Adresse hinzu:

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### Erstellen einer AAAA-Datensatzgruppe mit einem einzelnen Eintrag

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### Erstellen einer CNAME-Datensatzgruppe mit einem einzelnen Eintrag

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE]CNAME-Datensätze lassen nur einen einzelnen Zeichenfolgenwert zu.

### Erstellen einer MX-Datensatzgruppe mit einem einzelnen Eintrag

In diesem Beispiel verwenden wir den Namen des Datensatzes "@" zum Erstellen des MX-Eintrags auf oberster Ebene der Zone (z. B. "contoso.com"). Dies ist bei MX-Einträgen üblich.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### Erstellen einer NS-Datensatzgruppe mit einem einzelnen Eintrag

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### Erstellen einer SRV-Datensatzgruppe mit einem einzelnen Eintrag

Wenn Sie einen SRV-Eintrag im Zonenstamm erstellen, geben Sie einfach "\_service" und "\_protocol" im Eintragsnamen an. Sie müssen nicht zusätzlich ". @" im Eintragsnamen angeben.

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### Erstellen einer TXT-Datensatzgruppe mit einem einzelnen Eintrag

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## Ändern von vorhandenen Datensatzgruppen


Dies wird in den folgenden Beispielen gezeigt:

### Aktualisieren eines Eintrags in einer vorhandenen Datensatzgruppe

Für dieses Beispiel fügen wir eine andere IP-Adresse (1.2.3.4) zu einer vorhandenen A-Datensatzgruppe hinzu (www):

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


Verwenden Sie `azure network dns record-set delete-record`, um einen vorhandenen Wert aus einer Datensatzgruppe zu entfernen:
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Entfernen eines Eintrags aus einer vorhandenen Datensatzgruppe

Datensätze können mithilfe von `azure network dns record-set delete-record` aus einer Datensatzgruppe entfernt werden. Beachten Sie, dass der entfernte Eintrag für alle Parameter exakt mit einem vorhandenen Eintrag übereinstimmen muss.

Durch Entfernen des letzten Eintrags aus einer Datensatzgruppe wird die Datensatzgruppe nicht gelöscht. Weitere Informationen finden Sie nachfolgend unter [Löschen einer Datensatzgruppe](#delete-a-record-set).


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Entfernen eines AAAA-Eintrags aus einer Datensatzgruppe

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Entfernen eines CNAME-Eintrags aus einer Datensatzgruppe

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Entfernen eines MX-Eintrags aus einer Datensatzgruppe

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Entfernen eines NS-Eintrags aus einer Datensatzgruppe
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Entfernen eines SRV-Eintrags aus einer Datensatzgruppe

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Entfernen eines TXT-Eintrags aus einer Datensatzgruppe

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## Löschen einer Datensatzgruppe
Datensatzgruppen können mit dem Cmdlet "Remove-AzureDnsZone" gelöscht werden.

>[AZURE.NOTE]Sie können nicht die SOA- und NS-Datensatzgruppen an der Zonenspitze (Name = "@") löschen, die beim Erstellen der Zone automatisch erstellt werden. Sie werden automatisch gelöscht, wenn die Zone gelöscht wird.

Im folgenden Beispiel wird die A-Datensatzgruppe mit Namen "Test-a" aus der DNS-Zone von "contoso.com" entfernt:

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

Mit dem optionalen Switch "-q" kann diese Bestätigungsaufforderung unterdrückt werden.


##Siehe auch

[Erste Schritte beim Erstellen von Datensatzgruppen und Einträgen](dns-getstarted-create-recordset-cli.md)<BR> [Durchführen von Vorgängen für DNS-Zonen](dns-operations-dnszones-cli.md)<BR> [Automatisieren von Vorgängen mit dem .NET SDK](dns-sdk.md)
 

<!---HONumber=Oct15_HO3-->