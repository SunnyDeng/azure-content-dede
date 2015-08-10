<properties
   pageTitle="Erste Schritte mit Azure DNS | Microsoft Azure"
   description="Erfahren Sie, wie Sie DNS-Zonen für Azure DNS erstellen. Dies ist eine schrittweise Anleitung für die Erstellung der ersten DNS-Zone, um mit dem Hosten der DNS-Domäne mithilfe von PowerShell oder der Befehlszeilenschnittstelle zu beginnen."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="joaoma"/>

# Erste Schritte mit Azure DNS

Die Domäne "contoso.com" kann mehrere DNS-Einträge, wie z. B. "mail.contoso.com" \(für einen E-Mail-Server\) und "www.contoso.com" \(für eine Website\), enthalten. Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet.

Um mit dem Hosten der Domäne zu beginnen, müssen wir zunächst eine DNS-Zone erstellen. Alle DNS-Einträge, die für eine bestimmte Domäne erstellt wurden, befinden sich in einer DNS-Zone für die Domäne.

Für diese Anweisungen wird die Microsoft Azure-Befehlszeilenschnittstelle \(CLI\) verwendet. Achten Sie darauf, dass Sie auf das neueste Azure-Befehlszeilenschnittstelle aktualisieren, um die Azure DNS-Befehle zu verwenden.

## Einrichten der Azure-Befehlszeilenschnittstelle

### Schritt 1

Installieren Sie die Azure-Befehlszeilenschnittstelle. Sie können die Azure-Befehlszeilenschnittstelle für Windows, Linux oder Mac installieren. Die folgenden Schritte müssen ausgeführt werden, bevor Sie Azure DNS über die Azure-Befehlszeilenschnittstelle verwalten können. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md). Alle Netzwerkanbieterbefehle für die Befehlszeilenschnittstelle können über den folgenden Befehl ermittelt werden:

	Azure network 

### Schritt 2

Azure DNS verwendet den Azure-Ressourcen-Manager \(ARM\). Stellen Sie sicher, dass Sie die Befehlszeilenschnittstelle so einstellen, dass ARM-Befehle und DNS verwendet werden.

	Azure config mode arm

### Schritt 3

Melden Sie sich beim Azure-Konto an.

    Azure login -u "username"

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert. Denken Sie daran, dass Sie nur ORGID-Konten verwenden können.

### Schritt 4
Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

    Azure account set "subscription name"

Um eine Liste der verfügbaren Abonnements anzuzeigen, verwenden Sie den Befehl "azure account list".

### Schritt 5

Erstellen Sie eine neue Ressourcengruppe. \(Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.\)

    Azure group create -n myresourcegroup --location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Da alle DNS-Ressourcen global und nicht regional sind, hat die Auswahl des Speicherorts für die Ressourcengruppe jedoch keine Auswirkungen auf Azure DNS.

### Schritt 6

Der Azure DNS-Dienst wird vom Ressourcenanbieter "Microsoft.Network" verwaltet. Ihr Azure-Abonnement muss für die Verwendung dieses Ressourcenanbieters registriert werden, bevor Sie Azure DNS verwenden können. Dieser Schritt muss für jedes Abonnement einmal ausgeführt werden.

	Azure provider register --namespace Microsoft.Network

## Tags

Tags unterscheiden sich von Etags. Tags sind eine Liste von Name-Wert-Paaren, die vom Azure-Ressourcen-Manager zum Beschriften von Ressourcen zu Abrechnungs- oder Gruppierungszwecken verwendet werden. Weitere Informationen zu Tags finden Sie unter "Verwenden von Tags zum Organisieren von Azure-Ressourcen". Die Azure DNS-Befehlszeilenschnittstelle unterstützt Tags für Zonen und Datensatzgruppen, die mit den Optionsparametern "-Tag" angegeben wurden. Das folgende Beispiel zeigt, wie Sie eine DNS-Zone mit zwei Tags erstellen, "project = demo" und "env = test":

	Azure network dns-zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## Erstellen einer DNS-Zone

Eine DNS-Zone wird mit dem Befehl "azure network dns-zone create" erstellt. Im folgenden Beispiel erstellen wir eine DNS-Zone namens "contoso.com" in der Ressourcengruppe namens "MyResourceGroup":

    Azure network dns-zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]In Azure DNS sollten Zonennamen ohne abschließenden "." angegeben werden. Beispiel: "contoso.com" anstatt "contoso.com.".


Die DNS-Zone wurde nun in Azure DNS erstellt. Beim Erstellen einer DNS-Zone werden auch die folgenden DNS-Einträge erstellt:

Der "Start of Authority"-Eintrag \(SOA\). Dieser ist im Stamm jeder DNS-Zone vorhanden. Die autoritativen Namenservereinträge \(NS\). Diese zeigen, welche Namenserver die Zone hosten. Azure DNS verwendet einen Pool von Namenservern, sodass verschiedene Namenserver verschiedenen Zonen in Azure DNS zugewiesen werden können. Weitere Informationen finden Sie unter "Delegieren einer Domäne an Azure DNS". Um diese Datensätze anzuzeigen, verwenden Sie "azure network dns-record-set show":
   
	azure network dns-record-set show -g myresourcegroup --dns-zone "contoso.com" -n "@" --type SOA
	info:    Executing command network dns-record-set show
	DNS zone name: contoso.com
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Um die erstellten NS-Datensätze anzuzeigen, verwenden Sie den folgenden Befehl:

	azure network dns-record-set show -g myresourcegroup --dns-zone "contoso.com" -n "@" --type NS
	info:    Executing command network dns-record-set show
	DNS zone name: contoso.com
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE]Datensatzgruppen am Stamm \(oder der "Spitze"\) einer DNS-Zone verwenden "@" als Datensatzgruppennamen.

Nach der Erstellung Ihrer ersten DNS-Zone können Sie sie mit DNS-Tools wie nslookup, dig oder mit dem PowerShell-Cmdlet "Resolve-DnsName" testen. Wenn Sie Ihre Domäne noch nicht delegiert haben, um die neue Zone in Azure DNS zu verwenden, müssen Sie die DNS-Abfrage direkt auf einen der Namenserver für die Zone leiten. Die Namenserver für die Zone sind in den NS-Einträgen enthalten, die von "azure network dns-record-set show" aufgelistet werden. Ersetzen Sie im folgenden Befehl die Werte durch die für Ihre Zone ordnungsgemäßen Werte.

Im folgenden Beispiel wird DIG zum Abfragen der Domäne "contoso.com" mithilfe der Namenserver verwendet, die der DNS-Zone zugewiesen sind. Die Abfrage muss auf einem Namenserver, den wir verwendet haben \(`@<name server for the zone>`\), und den Zonenname mithilfe von DIG verweisen.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net. 
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120
	
## Nächste Schritte

<!---HONumber=July15_HO5-->