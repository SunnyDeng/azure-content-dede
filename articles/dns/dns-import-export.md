<properties
   pageTitle="Importieren und Exportieren eine Domänenzonendatei mit Azure DNS | Microsoft Azure"
   description="Informationen zum Importieren und Exportieren einer DNS-Zonendatei in Azure DNS mithilfe der Azure-CLI"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="jonatul"/>

# Importieren und Exportieren einer DNS-Zonendatei


In diesem Leitfaden wird das Importieren und Exportieren von DNS-Zonendateien in und aus Azure DNS erläutert.

## Einführung in die DNS-Zonenmigration

Eine DNS-Zonendatei ist eine Textdatei, die Details aller DNS-Einträge in der Zone enthält. Sie hat ein Standardformat, weshalb sie sich für das Übertragen von DNS-Einträgen zwischen verschiedenen DNS-Systemen eignet. Das Verwenden einer Zonendatei ist eine schnelle, zuverlässige und praktische Möglichkeit, eine DNS-Zone in und aus Azure DNS zu übertragen.

Azure DNS unterstützt das Importieren und Exportieren von Zonendatei über die Azure-CLI. In diesem Artikel wird erläutert, wie dieses Feature verwendet wird.

Die Azure-CLI (Command Line Interface, Befehlszeilenschnittstelle) ist ein plattformübergreifendes Befehlszeilentool zum Verwalten von Azure-Diensten. Sie steht auf der [Azure-Downloadseite](https://azure.microsoft.com/downloads/) für Windows-, Mac- und Linux-Plattformen zur Verfügung. Diese plattformübergreifende Unterstützung ist besonders wichtig für das Importieren und Exportieren von Zonendateien, da die am häufigsten verwendete Namenserversoftware ([BIND](https://www.isc.org/downloads/bind/)) meist unter Linux ausgeführt wird.

## Abrufen Ihrer vorhandenen DNS-Zonendatei

Bevor Sie die DNS-Zonendatei in Azure DNS importieren, müssen Sie eine Kopie der Zonendatei abrufen. Der Speicherort dieser Datei hängt davon ab, wo die DNS-Zone derzeit gehostet wird. Beispiel:

- Wenn Ihre DNS-Zone von einem Drittanbieterdienst (z. B. einer Domänenregistrierungsstelle, einem dedizierten DNS-Hoster oder anderen Cloudanbieter) gehostet wird, sollte der Dienst die Möglichkeit zum Herunterladen der DNS-Zonendatei bieten.
-	Wenn die DNS-Zone mithilfe eines Windows-DNS-Servers gehostet wird, befinden sich die Zonendateien standardmäßig im Ordner **%systemroot%\\system32\\dns**. Der vollständige Pfad zu jeder Zonendatei wird auch in der DNS-Dienstverwaltungskonsole auf der Registerkarte „Allgemein“ angezeigt.
-	Wenn Ihre DNS-Zone mithilfe von BIND gehostet wird, wird der Speicherort der Zonendatei für jede Zone in der BIND-Konfigurationsdatei **named.conf** angegeben. 

>[AZURE.NOTE]Von „GoDaddy“ heruntergeladene Zonendateien haben ein nicht ganz standardmäßiges Format, das korrigiert werden muss, bevor Sie diese Zonendateien in Azure DNS importieren. Insbesondere werden DNS-Namen im Abschnitt RDATA für jeden DNS-Eintrag als voll qualifizierte Namen, aber ohne abschließenden „.“ angegeben, was heißt, dass diese Einträge von anderen DNS-Systemen als relative Namen interpretiert werden. Sie müssen die Zonendatei bearbeiten, indem Sie den abschließenden „.“ an diese Namen anfügen, bevor Sie sie in Azure DNS importieren.

## Importieren einer DNS-Zonendatei in Azure DNS

Das Format des Azure-CLI-Befehls zum Importieren einer DNS-Zone lautet wie folgt: `azure network dns zone import [options] <resource group> <zone name> <zone file name>`, wobei:

- **resource group** der Name der Ressourcengruppe für die Zone in Azure DNS ist.
- **zone name** der Name der Zone ist.
- **zone file name** der Pfad/Name der zu importierenden Zonendatei ist.

Wenn Sie eine Zone mit diesem Namen in der Ressourcengruppe nicht vorhanden ist, wird sie für Sie erstellt. Wenn die Zone bereits vorhanden ist, werden die importierten Ressourceneintragssätze mit vorhandenen Ressourceneintragssätzen zusammengeführt. Um die vorhandenen Ressourceneintragssätze zu überschreiben, verwenden Sie die Option „--force“. Weitere Informationen dazu, [wie Ressourceneintragssätze zusammengeführt werden](#merging-with-existing-data), finden Sie nachstehend.

Um das Format einer Zonendatei zu überprüfen, ohne sie tatsächlich zu importieren, verwenden Sie die Option „--parse-only“.

## Auszuführende Schritte zum Importieren einer Zonendatei in Azure DNS

Sehen wir uns ein Beispiel an. Angenommen, Sie möchten eine Zonendatei für die Zone „contoso.com“ importieren.

### Schritt 1
Melden Sie sich über die Azure-CLI bei Ihrem Azure-Abonnement an.

	azure login

### Schritt 2
Wählen Sie das Abonnement aus, in dem Sie die neue DNS-Zone erstellen möchten.

	azure account set <subscription name>

### Schritt 3
Azure DNS ist ein auf Azure-Ressourcen-Manager (ARM) beschränkter Dienst, weshalb die Azure-CLI in den ARM-Modus umgeschaltet werden muss.

	azure config mode arm

### Schritt 4
Bevor Sie den Azure DNS-Dienst verwenden, müssen Sie Ihr Abonnement für die Verwendung des Ressourcenanbieters „Microsoft.Network“ registrieren (was für jedes Abonnement nur einmal erfolgen muss).

	azure provider register Microsoft.Network

### Schritt 5 
Falls noch nicht vorhanden, müssen Sie auch eine ARM-Ressourcengruppe erstellen.
	
	azure group create myresourcegroup westeurope

### Schritt 6	
Um die Zone „contoso.com“ aus der Datei „contoso.com.txt“ in eine neue DNS-Zone in der Ressourcengruppe „myresourcegroup“ zu importieren, führen Sie den Befehl `azure network dns zone import` aus.

	azure network dns zone import myresourcegroup contoso.com contoso.com.txt

Dieser Befehl lädt die Zonendatei, analysiert sie und wendet eine Reihe von Befehlen auf den Azure DNS-Dienst an, damit die Zone und alle Ressourceneintragssätze in der Zone erstellt werden.

Der Befehl meldet im Konsolenfenster den Status sowie etwaige Fehler oder Warnungen. Da Ressourceneintragssätze in Reihe erstellt werden, dauert der Import einer großen Zonendatei einige Minuten.

## Überprüfen der DNS-Zone nach dem Import

Sie können die Einträge mit dem folgenden Azure-CLI-Befehl auflisten (hierzu können Sie auch den PowerShell-Befehl `Get-AzureRmDnsRecordSet` verwenden).

	azure network dns record-set list myresourcegroup contoso.com

Sie können auch „nslookup“ verwenden, um die Namensauflösung für die Einträge zu überprüfen. Da die Zone nicht noch delegiert ist, müssen Sie die richtigen Azure DNS-Namenserver explizit angeben. Das folgende Beispiel veranschaulicht das Abrufen der Namen der Namenserver, die der Zone zugewiesen sind, und fragt den „www“-Eintrag mittels „nslookup“ ab:

	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
	info:    Executing command network dns record-set show
	+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com
	data:        Name server domain name     : ns2-01.azure-dns.net
	data:        Name server domain name     : ns3-01.azure-dns.org
	data:        Name server domain name     : ns4-01.azure-dns.info
	data:
	info:    network dns record-set show command OK

	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  40.90.4.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
            134.170.188.221

Nachdem Sie überprüft haben, dass die Zone ordnungsgemäß importiert wurde, müssen Sie die [DNS-Delegierung so aktualisieren](dns-domain-delegation.md), dass auf die Azure DNS-Namenserver gezeigt wird.

## Zusammenführen mit vorhandenen Daten

Beim Importieren einer Zonendatei wird eine neue Zone in Azure DNS erstellt, wenn noch keine vorhanden ist. Wenn die Zone bereits vorhanden ist, werden die Ressourceneintragssätze in der Zonendatei mit den vorhandenen Ressourceneintragssätzen zusammengeführt. Das Zusammenführungsverhalten ist wie folgt:

1.	Standardmäßig werden vorhandene und neue Ressourceneintragssätze zusammengeführt. Identische Datensätze innerhalb eines zusammengeführten Ressourceneintragssatzes werden dedupliziert.
2.	Alternativ ersetzt der Importvorgang bei Angabe der Option „--force“ vorhandene Ressourceneintragssätze durch neue Ressourceneintragssätze. Vorhandene Ressourceneintragssätze, die keinen entsprechenden Ressourceneintragssatz in der importierten Zonendatei haben, werden nicht entfernt.
3.	Wenn Ressourceneintragssätze zusammengeführt werden, wird die Gültigkeitsdauer bereits vorhandener Ressourceneintragssätze verwendet. Bei Verwenden von „--force“ wird die Gültigkeitsdauer des neuen Ressourceneintragssatzes verwendet.
4.	SOA-Parameter (mit Ausnahme von „host“) werden unabhängig von der Verwendung von „--force“ immer der importierten Zonendatei entnommen. Auch für den NS-Ressourceneintragssatz an der Zonenspitze wird die Gültigkeitsdauer stets der importierten Zonendatei entnommen.
5.	Ein importierter CNAME-Eintrag ersetzt keinen vorhandenen CNAME-Eintrag mit dem gleichen Namen, es sei denn, „--force“ wurde angegeben.
6.	Wenn ein Konflikt zwischen einem CNAME-Eintrag und einen weiteren Eintrag mit demselben Namen, aber einem anderen Typ entsteht (unabhängig davon, welcher vorhanden oder neu ist), bleibt der vorhandene Eintrag erhalten. Dies ist unabhängig von der Verwendung von „--force“.

## Weitere technische Details
Es folgen weitere technische Details zum Zonenimportprozess:

1.	Die Direktive „$TTL“ ist optional und wird unterstützt. Wird keine „$TTL“-Direktive angegeben, werden Einträge ohne explizite Gültigkeitsdauer mit dem Standard-TTL-Wert 3.600 Sekunden importiert. Wenn bei zwei Einträgen im selben Ressourceneintragssatz eine unterschiedliche Gültigkeitsdauer angegeben ist, wird der niedrigere Wert verwendet.
2.	Die Direktive „$ORIGIN“ ist optional und wird unterstützt. Wird „$ORIGIN“ nicht festgelegt, ist der verwendete Standardwert der Zonenname gemäß Angabe in der Befehlszeile (plus abschließendem „.“).
3.	Die Direktiven „$INCLUDE“ und „$GENERATE“ werden nicht unterstützt.
4.	Die folgenden Eintragstypen werden unterstützt: A, AAAA, CNAME, MX, NS, SOA, SRV und TXT.  
5.	Der SOA-Eintrag wird von Azure DNS automatisch erstellt, wenn eine Zone erstellt wird. Beim Importieren einer Zonendatei stammen alle SOA-Parameter aus der Zonendatei mit Ausnahme des „host“-Parameters, der den von Azure DNS bereitgestellten Wert verwendet. Der Grund ist, dass dieser Parameter auf den von Azure DNS bereitgestellten primären Namenserver verweisen muss.
6.	Der NS-Ressourceneintragssatz an der Zonenspitze wird auch automatisch von Azure DNS beim Erstellen der Zone erstellt. Nur die Gültigkeitsdauer dieses Ressourceneintragssatzes wird importiert. Diese Einträge enthalten die von Azure DNS bereitgestellten Namen der Namenserver, weshalb die Eintragsdaten nicht von den Werten überschrieben werden, die in der importierten Zonendatei enthalten sind.
7.	In der öffentlichen Vorschauversion unterstützt Azure DNS nur TXT-Einträge mit einer einzelnen Zeichenfolge, weshalb TXT-Einträge mit mehreren Zeichenfolgen verkettet und bei 255 Zeichen abgeschnitten werden.

## Exportieren einer DNS-Zonendatei aus Azure DNS

Das Format des Azure-CLI-Befehls zum Exportieren einer DNS-Zone lautet wie folgt: `azure network dns zone export [options] <resource group> <zone name> <zone file name>`, wobei:

- **resource group** der Name der Ressourcengruppe für die Zone in Azure DNS ist.
- **zone name** der Name der Zone ist.
- **zone file name** der Pfad/Name der zu exportierenden Zonendatei ist.

## Auszuführende Schritte zum Exportieren einer Azure DNS-Datei
 
Wie beim Zonenimport müssen Sie sich zunächst anmelden, Ihr Abonnement auswählen und die Azure-CLI für den Verwendung des ARM-Modus konfigurieren:

### Schritt 1
Melden Sie sich über die Azure-CLI bei Ihrem Azure-Abonnement an.

	azure login

### Schritt 2
Wählen Sie das Abonnement aus, in dem Sie die neue DNS-Zone erstellen möchten.

	azure account set <subscription name>

### Schritt 3
Azure DNS ist ein auf Azure-Ressourcen-Manager (ARM) beschränkter Dienst, weshalb die Azure-CLI in den ARM-Modus umgeschaltet werden muss.

	azure config mode arm
### Schritt 4
Um die vorhandenen Azure-DNS-Zone „contoso.com“ in die Ressourcengruppe „myresourcegroup“ in die Datei „contoso.com.txt“ (im aktuellen Ordner) zu exportieren, führen Sie `azure network dns zone export` aus.

	azure network dns zone export myresourcegroup contoso.com contoso.com.txt

Mit diesem Befehl wird der Azure DNS-Dienst aufgefordert, die Ressourceneintragssätze in der Zone aufzuzählen und die Ergebnisse in eine mit BIND kompatible Zonendatei zu exportieren.

<!---HONumber=AcomDC_0114_2016-->