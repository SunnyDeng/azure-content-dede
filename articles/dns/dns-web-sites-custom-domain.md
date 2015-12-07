<properties 
   pageTitle="Erstellen von benutzerdefinierten DNS-Einträgen für eine Web-App | Microsoft Azure" 
   description="Erstellen von benutzerdefinierten Domänen-DNS-Einträgen für die Web-App mithilfe von Azure DNS. Schrittweise Anleitung zum Überprüfen des Domänenbesitzers mithilfe des CNAME- oder A-Eintrags" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Erstellen von DNS-Einträgen für eine Web-App in einer benutzerdefinierten Domäne

Sie können Azure DNS verwenden, um eine benutzerdefinierte Domäne für Ihre Web-Apps zu hosten. Stellen Sie sich z. B. vor, dass Sie eine Azure-Web-App erstellen und dass Ihre Benutzer über contoso.com oder www.contoso.com als vollqualifizierten Domänennamen Zugriff darauf haben sollen. In diesem Szenario müssen Sie zwei Einträge erstellen: einen A-Stammeintrag, der auf "contoso.com" verweist, und einen CNAME-Eintrag für den www-Namen, der auf den A-Eintrag verweist.

> [AZURE.NOTE]Beachten Sie, dass Sie beim Erstellen eines A-Eintrags für eine Web-App in Azure den A-Eintrag manuell aktualisieren müssen, wenn sich die zugrunde liegende IP-Adresse für die Web-App ändert.

Bevor Sie Einträge für Ihre benutzerdefinierte Domäne erstellen können, müssen Sie eine DNS-Zone in Azure DNS erstellen und die Zone in Ihrer Registrierungsstelle an Azure DNS delegieren. Befolgen Sie zum Erstellen einer DNS-Zone die Anweisungen unter [Erste Schritte mit Azure DNS](../dns-getstarted-create-dnszone/#Create-a-DNS-zone). Befolgen Sie zum Delegieren Ihres DNS an Azure DNS den Anweisungen unter [Delegieren einer Domäne an Azure DNS](../dns-domain-delegation).
 
## Hinzufügen eines A-Eintrags für Ihre benutzerdefinierte Domäne

Ein A-Eintrag wird verwendet, um der IP-Adresse einen Namen zuzuordnen. Im folgenden Beispiel wird "@" als A-Eintrag einer IPv4-Adresse zugewiesen:

### Schritt 1
 
Erstellen Sie einen A-Eintrag, und weisen Sie ihn einer Variablen "$rs" zu.
	
	PS C:\>$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Schritt 2

Fügen Sie einen IPv4-Wert der zuvor erstellten Datensatzgruppe "@" mithilfe der zugewiesenen $rs-Variablen hinzu. Der zugewiesene IPv4-Wert ist die IP-Adresse für Ihre Web-App.

> [AZURE.NOTE]Befolgen Sie zum Suchen der IP-Adresse für eine Web-App die Anleitungen unter [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service](../web-sites-custom-domain-name/#Find-the-virtual-IP-address).

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Schritt 3

Übergeben Sie die Änderungen an die Datensatzgruppe. Verwenden Sie „Set-AzureRmDnsRecordSet“, um Änderungen an der Datensatzgruppe in Azure DNS hochzuladen:

	Set-AzureRMDnsRecordSet -RecordSet $rs

## Erstellen eines CNAME-Eintrags für Ihre benutzerdefinierte Domäne

Wenn Ihre Domäne bereits von Azure DNS verwaltet wird (siehe [DNS-Domänendelegierung](../dns-domain-delegation)), können Sie das folgende Beispiel verwenden, um einen CNAME-Eintrag für contoso.azurewebsites.net zu erstellen:

### Schritt 1

Öffnen Sie PowerShell, und erstellen Sie eine neue CNAME-Datensatzgruppe, und weisen Sie sie einer Variablen "$rs" zu:

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

Dadurch wird ein Datensatzgruppentyp "CNAME" mit einer Gültigkeitsdauer von 600 Sekunden in der DNS-Zone mit dem Namen "contoso.com" erstellt.

### Schritt 2

Sobald die CNAME-Datensatzgruppe erstellt wurde, müssen Sie einen Aliaswert erstellen, der auf die Web-App verweist.

Mit der zuvor zugewiesenen Variable "$rs" können Sie den folgenden PowerShell-Befehl verwenden, um den Alias für die Web-App "contoso.azurewebsites.net" zu erstellen.

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Schritt 3

Führen Sie mithilfe des Cmdlets „Set-AzureRmDnsRecordSet“ ein Commit für die Änderungen aus.

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

Sie können überprüfen, ob der Eintrag korrekt erstellt wurde, indem Sie "www.contoso.com" mit nslookup abfragen, wie unten dargestellt:

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## Erstellen eines awverify-Eintrags für Web-Apps (nur A-Einträge)

Wenn Sie einen A-Datensatz für Ihre Web-App verwenden möchten, müssen Sie einen Überprüfungsprozess durchlaufen, um sicherzustellen, dass Sie die benutzerdefinierte Domäne besitzen. Dieser Überprüfungsschritt erfolgt durch Erstellen eines speziellen CNAME-Eintrags mit dem Namen "awverify".

Im folgenden Beispiel wird der Eintrag "awverify" für "contoso.com" erstellt, um den Besitzer für die benutzerdefinierte Domäne zu überprüfen:

### Schritt 1

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Schritt 2

Sobald der Eintrag "awverify" erstellt wurde, müssen Sie den CNAME-Datensatzgruppenalias auf "awverify.contoso.azurewebsites.net" festlegen, wie im folgenden Befehl gezeigt:

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Schritt 3

Führen Sie mithilfe des Cmdlets „Set-AzureRmDnsRecordSet“ ein Commit für die Änderungen aus, wie im folgenden Befehl gezeigt:

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

Jetzt können Sie mit der Durchführung der Schritte unter [Konfigurieren eines benutzerdefinierten Domänennamens für App Service](../web-sites-custom-domain-name) fortfahren, um die Web-App so zu konfigurieren, dass eine benutzerdefinierte Domäne verwendet wird.

## Siehe auch

[Verwalten von DNS-Zonen](../dns-operations-dnszones)

[Verwalten von DNS-Einträgen](../dns-operations-recordsets)

[Traffic Manager – Übersicht](../traffic-manager-overview)

[Automatisieren von Azure-Vorgängen mit dem .NET SDK](../dns-sdk)


 

<!---HONumber=AcomDC_1125_2015-->