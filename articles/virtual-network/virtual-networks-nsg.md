<properties 
   pageTitle="Was ist eine Netzwerksicherheitsgruppe (NSG)?"
   description="Erfahren Sie mehr zur verteilten Firewall in Azure mit Netzwerksicherheitsgruppen (NSGs) und zur Verwendung von NSGs zum Isolieren und Steuern von Datenverkehr in Ihren virtuellen Netzwerken (VNets)."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="telmos" />

# Was ist eine Netzwerksicherheitsgruppe (NSG)?

Eine Netzwerksicherheitsgruppe (NSG) enthält eine Zugriffssteuerungsliste (Access Control List, ACL) zum Zulassen oder Verweigern von Netzwerkdatenverkehr an Ihre VM-Instanzen in einem virtuellen Netzwerkwerk. NSGs können Subnetzen oder einzelnen VM-Instanzen innerhalb dieses Subnetzes zugeordnet werden. Wenn eine NSG einem Subnetz zugeordnet ist, gelten die ACL-Regeln für alle VM-Instanzen in diesem Subnetz. Darüber hinaus kann Datenverkehr zu einer einzelnen virtuellen Maschine weiter beschränkt werden, indem eine NSG direkt diesem virtuellen Computer zugewiesen wird.

## NSG-Ressource

NSGs haben die folgenden Eigenschaften:

|Eigenschaft|Beschreibung|Einschränkungen|Überlegungen|
|---|---|---|---|
|Name|Name der NSG|Muss innerhalb der Region eindeutig sein.<br/>Kann Buchstaben, Zahlen, Unterstriche, Punkte und Bindestriche enthalten.<br/>Muss mit einem Buchstaben oder einer Zahl beginnen.<br/>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich enden.<br/>Kann bis zu 80 Zeichen enthalten.|Da Sie u. U. eine ganze Reihe von NSGs erstellen müssen, sollten Sie durch Ihre Namenskonvention sicherstellen, dass sich die Funktion der NSGs leicht am Namen erkennen lässt.|
|Region|Die Azure-Region, in der die NSG gehostet wird|NSGs können nur auf Ressourcen innerhalb der Region angewendet werden, in der sie erstellt wurden.|Lesen Sie unten unter [Einschränkungen](#Limits), wie viele NSGs Sie in einer Region nutzen können.|
|Ressourcengruppe|Die Ressourcengruppe, zu der die NSG gehört|Obwohl eine NSG zu einer Ressourcengruppe gehört, kann sie Ressourcen in beliebigen Ressourcengruppen zugeordnet werden, sofern die Ressource zu derselben Azure-Region gehört wie die NSG.|Ressourcengruppen werden verwendet, um mehrere Ressourcen gemeinsam als eine Bereitstellungseinheit zu verwalten.<br/>Unter Umständen ist es ratsam, NSGs mit den Ressourcen zu gruppieren, denen sie zugeordnet sind.|
|Regeln|Regeln definieren, welcher Datenverkehr zugelassen oder verweigert wird.||Siehe Abschnitt [NSG-Regeln](#Nsg-rules) unten.| 

>[AZURE.NOTE] Endpunktbasierte ACLs und Netzwerksicherheitsgruppen können nicht für die gleiche VM-Instanz verwendet werden. Wenn Sie eine NSG verwenden möchten und bereits eine Endpunkt-ACL eingerichtet ist, entfernen Sie zuerst die Endpunkt-ACL. Informationen zur Vorgehensweise finden Sie unter [Verwalten von Zugriffssteuerungslisten (ACLs) für Endpunkte mithilfe von PowerShell](virtual-networks-acl-powershell.md).

### NSG-Regeln

NSG-Regeln haben die folgenden Eigenschaften:

|Eigenschaft|Beschreibung|Einschränkungen|Überlegungen|
|---|---|---|---|
|**Name**|Name der Regel|Muss innerhalb der Region eindeutig sein.<br/>Kann Buchstaben, Zahlen, Unterstriche, Punkte und Bindestriche enthalten.<br/>Muss mit einem Buchstaben oder einer Zahl beginnen.<br/>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich enden.<br/>Kann bis zu 80 Zeichen enthalten.|Da eine NSG mehrere Regeln enthalten kann, sollten Sie durch Ihre Namenskonvention sicherstellen, dass sich die Funktion der Regel leicht am Namen erkennen lässt.|
|**Protokoll**|Protokoll entsprechend der Regel|TCP, UDP oder *|Wenn Sie das Protokoll mit * angeben, schließt dies ICMP (nur Ost-West-Datenverkehr) sowie UDP und TCP ein. Auf diese Weise können Sie u. U. die Anzahl der benötigten Regeln reduzieren.<br/>Gleichzeitig kann die Angabe von * zu allgemein sein. Verwenden Sie diesen Ansatz deshalb nur, wenn er wirklich nötig ist.|
|**Quellportbereich**|Quellportbereich entsprechend der Regel|Eine einzelne Portnummer von 1 bis 65535, ein Portbereich (z.B. 1-65635) oder * (für alle Ports)|Quellports könnte kurzlebig sein. Sofern Ihr Clientprogramm keinen bestimmten Port nutzt, verwenden Sie in den meisten Fällen „*“.<br/>Versuchen Sie, weitestgehend mit Portbereichen zu arbeiten, um die Anzahl von notwendigen Regeln zu reduzieren.<br/>Mehrere Ports oder Portbereiche können nicht mit Kommas gruppiert werden. 
|**Zielportbereich**|Zielportbereich entsprechend der Regel|Eine einzelne Portnummer von 1 bis 65535, ein Portbereich (z.B. 1-65535) oder * (für alle Ports)|Versuchen Sie, weitestgehend mit Portbereichen zu arbeiten, um die Anzahl von notwendigen Regeln zu reduzieren.<br/>Mehrere Ports oder Portbereiche können nicht mit Kommas gruppiert werden
|**Quelladresspräfix**|Quelladresspräfix oder -tag entsprechend der Regel|Eine einzelne IP-Adresse (z. B. 10.10.10.10), ein IP-Subnetz (z. B. 192.168.1.0/24), ein [Standardtag](#Default-Tags) oder * (für alle Adressen)|Verwenden Sie nach Möglichkeit Bereiche, Standard-Tags und *, um die Anzahl von Regeln zu reduzieren.|
|**Zieladresspräfix**|Zieladresspräfix oder -tag entsprechend der Regel|Eine einzelne IP-Adresse (z. B. 10.10.10.10), ein IP-Subnetz (z. B. 192.168.1.0/24), ein [Standardtag](#Default-Tags) oder * (für alle Adressen)|Verwenden Sie nach Möglichkeit Bereiche, Standard-Tags und *, um die Anzahl von Regeln zu reduzieren.|
|**Richtung**|Richtung des Datenverkehrs entsprechend der Regel|Eingehend oder ausgehend|Die Regeln für eingehenden und ausgehenden Datenverkehr werden getrennt verarbeitet, abhängig von der Richtung.|
|**Priority**|Regeln werden in der Reihenfolge ihrer Priorität überprüft. Sobald eine Regel erfüllt ist, werden keine Übereinstimmungen mit weiteren Regeln gesucht.|Eine Zahl zwischen 100 und 4096|Vergeben Sie beim Erstellen von Regeln die Prioritäten am besten jeweils in Hunderterschritten, damit noch Platz für später hinzukommende Regeln bleibt, die von der Priorität her zwischen die bestehenden Regeln eingeordnet werden müssen.|
|**Access**|Typ des Zugriffs bei Übereinstimmung mit der Regel|Zulassen oder verweigern|Beachten Sie, dass ein Paket verworfen wird, wenn keine Zulassungsregel dafür gefunden wird.|

NGSs enthalten zwei Regelsätze: eingehende und ausgehende Regeln. Die Priorität für eine Regel muss innerhalb jedes Satzes eindeutig sein.

![Verarbeitung von NSG-Regeln](./media/virtual-network-nsg-overview/figure3.png)

Die Abbildung oben zeigt, wie die NSG-Regeln verarbeitet werden.

### Standardtags

Standardtags sind vom System bereitgestellte Bezeichner für eine Kategorie von IP-Adressen. In den Eigenschaften **Quelladresspräfix** und **Zieladresspräfix** von Regeln können Standardtags verwendet werden. Es gibt drei Standard-Tags, die Sie verwenden können.

- **VIRTUAL\_NETWORK**: Standardtag für Ihren gesamten Netzwerkadressraum. Dieser umfasst den Adressraum des virtuellen Netzwerks (in Azure definierte CIDR-Bereiche) sowie den gesamten verbundenen lokalen Adressraum und verbundene Azure VNets (lokale Netzwerke).

- **AZURE\_LOADBALANCER**: Standardtag für den Infrastruktur-Load Balancer von Azure. Wird in eine Azure-Datencenter-IP umgewandelt, die als Ausgangspunkt für die Integritätstests von Azure fungiert.

- **INTERNET**: Standardtag für den IP-Adressraum, der außerhalb des virtuellen Netzwerks liegt und über das öffentliche Internet erreichbar ist. Dieser Bereich schließt auch den [Azure-eigenen öffentlichen IP-Adressraum](https://www.microsoft.com/download/details.aspx?id=41653) mit ein.

### Standardregeln

Alle NSGs enthalten eine Gruppe von Standardregeln. Die Standardregeln können zwar nicht gelöscht werden, haben aber niedrigste Priorität und können somit durch selbst erstellte Regeln außer Kraft gesetzt werden.

Wie in den folgenden Standardregeln zu sehen, wird Datenverkehr aus einem bzw. in ein virtuelles Netzwerk in ein- und ausgehender Richtung zugelassen. In ausgehender Richtung wird die Verbindung mit dem Internet zugelassen, in eingehender Richtung wird sie dagegen standardmäßig blockiert. Eine der Standardregeln ermöglicht dem Load Balancer von Azure die Überprüfung der Integrität der virtuellen Computer und der Rolleninstanzen. Sie können diese Regel außer Kraft setzen, wenn Sie keine Gruppe mit Lastenausgleich verwenden.

**Eingehende Standardregeln**

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET INBOUND | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | ZULASSEN |
| ALLOW AZURE LOAD BALANCER INBOUND | 65001 | AZURE\_LOADBALANCER | * | * | * | * | ZULASSEN |
| DENY ALL INBOUND | 65500 | * | * | * | * | * | VERWEIGERN |

**Ausgehende Standardregeln**

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET OUTBOUND | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | ZULASSEN |
| ALLOW INTERNET OUTBOUND | 65001 | * | * | INTERNET | * | * | ZULASSEN |
| DENY ALL OUTBOUND | 65500 | * | * | * | * | * | VERWEIGERN |

## Zuordnen von NSGs

Je nach verwendetem Bereitstellungsmodell können Sie eine NSG einem virtuellen Computer, Netzwerkkarten (NICs) und Subnetzen zuordnen.

[AZURE.INCLUDE [learn-about-deployment-models-both-include.md](../../includes/learn-about-deployment-models-both-include.md)]
 
- **Zuordnen einer NSG zu einem virtuellen Computer (nur klassische Bereitstellungen).** Wenn Sie eine NSG einem virtuellen Computer zuordnen, werden die Netzwerkzugriffsregeln in der NSG auf jeglichen Datenverkehr angewendet, der für den virtuellen Computer bestimmt ist oder diesen verlässt. 

- **Zuordnen einer NSG zu einer Netzwerkkarte (nur Ressourcen-Manager-Bereitstellungen).** Wenn Sie eine NSG zu einer Netzwerkkarte zuordnen, werden die Netzwerkzugriffsregeln in der NSG nur auf diese Netzwerkkarte angewendet. Für einen virtuellen Computer mit mehreren Netzwerkkarten bedeutet dies, dass sich eine NSG, die auf eine einzelne Netzwerkkarte angewendet wird, nicht auf die anderen Netzwerkkarten auswirkt.

- **Zuordnen einer NSG zu einem Subnetz (alle Bereitstellungen)**. Wenn Sie eine NSG einem Subnetz zuordnen, werden die Netzwerk-Zugriffsregeln in der NSG auf alle IaaS- und PaaS-Ressourcen im Subnetz angewendet.

Sie können verschiedene NSGs einem virtuellen Computer (oder einer Netzwerkkarte, abhängig vom Bereitstellungsmodell) und dem Subnetz zuordnen, an das eine Netzwerkkarte oder ein virtueller Computer gebunden ist. In diesem Fall werden alle Netzwerkzugriffsregeln in der Reihenfolge Ihrer Priorität in der NSG auf den Datenverkehr angewendet:

- **Eingehender Datenverkehr**
	1. Auf das Subnetz angewendete NSG 
	
        Wenn eine Subnetz-NSG über eine Abgleichsregel zum Verweigern von Datenverkehr verfügt, wird das Paket hier abgelegt.
	2. Auf die Netzwerkkarte angewendete NSG (Ressourcen-Manager) oder auf den virtuellen Computer angewendete NSG (klassisch) 
	   
        Wenn die NSG für den virtuelle Computer/die Netzwerkkarte über eine Abgleichsregel zum Verweigern von Datenverkehr verfügt, wird das Paket auf dem virtuellen Computer/der Netzwerkkarte abgelegt, auch wenn die Subnetz-NSG über eine Abgleichsregel zum Zulassen von Datenverkehr verfügt.
- **Ausgehender Datenverkehr**
	1. Auf die Netzwerkkarte angewendete NSG (Ressourcen-Manager) oder auf den virtuellen Computer angewendete NSG (klassisch) 
	  
        Wenn eine NSG für den virtuelle Computer/die Netzwerkkarte über eine Abgleichsregel zum Verweigern von Datenverkehr verfügt, wird das Paket hier abgelegt.
	2. Auf das Subnetz angewendete NSG
	   
           Wenn eine Subnetz-NSG über eine Abgleichsregel zum Verweigern von Datenverkehr verfügt, wird das Paket hier abgelegt, auch wenn die NSG für den virtuelle Computer/die Netzwerkkarte über eine Abgleichsregel zum Zulassen von Datenverkehr verfügt.

![NSG-ACLs](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE] Sie können einem Subnetz, einem virtuellen Computer oder einer Netzwerkkarte zwar nur eine einzelne NSG zuordnen, Sie können eine solche NSG aber beliebig vielen Ressourcen zuordnen.

## Implementierung
Sie können NSGs mit den verschiedenen unten aufgeführten Tools im klassischen oder im Ressourcen-Manager-Bereitstellungsmodell implementieren.

|Bereitstellungstool|Klassisch|Ressourcen-Manager|
|---|---|---|
|Klassisches Portal|![Nein][red]|![Nein][red]|
|Azure-Portal|![Ja][green]|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-pportal">![Ja][green]</a>|
|PowerShell|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-ps">![Ja][green]</a>|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-ps">![Ja][green]</a>|
|Azure-Befehlszeilenschnittstelle|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-cli">![Ja][green]</a>|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-cli">![Ja][green]</a>|
|ARM-Vorlage|![Nein][red]|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-template">![Ja][green]</a>|

|**Schlüssel**|![Ja][green] Unterstützt Klicken Sie, um den Artikel aufzurufen.|![Nein][red] Nicht unterstützt|
|---|---|---|

## Planung

Bevor Sie NSGs implementieren, müssen Sie folgende Fragen beantworten:

1. Für welche Arten von Ressourcen möchten Sie eingehenden oder ausgehenden Datenverkehr filtern (für Netzwerkkarten in demselben virtuellen Computer, für virtuelle Computer oder andere Ressourcen wie Clouddienste oder Anwendungsdienstumgebungen, die mit demselben Subnetz verbunden sind, oder für Datenverkehr zwischen Ressourcen, die mit verschiedenen Subnetzen verbunden sind)?

2. Sind die Ressourcen, für die Sie eingehenden oder ausgehenden Datenverkehr filtern möchten, mit Subnetzen in vorhandenen VNETs verbunden, oder werden sie mit neuen VNETs oder Subnetzen verbunden?
 
Weitere Informationen zum Planen der Netzwerksicherheit in Azure finden Sie in den [bewährten Verfahren für Clouddienste und Netzwerksicherheit](../best-practices-network-security.md).

## Überlegungen zum Entwurf

Wenn Sie die Antworten auf die Fragen im Abschnitt [Planung](#Planning) kennen, machen Sie sich mit dem Folgenden vertraut, bevor Sie Ihre NSGs definieren.

### Grenzen

Sie müssen die folgenden Begrenzungen berücksichtigen, wenn Sie Ihre NSGs entwerfen.

|**Beschreibung**|**Standardlimit**|**Implikationen**|
|---|---|---|
|Anzahl von NSGs, die Sie einem Subnetz, einem virtuellen Computer oder einer Netzwerkkarte zuordnen können|1|Das bedeutet, dass Sie NSGs nicht kombinieren können. Stellen Sie sicher, dass sich alle für eine Gruppe von Ressourcen benötigten Regeln in einer einzigen NSG befinden.|
|NSGs pro Region und Abonnement|100|Standardmäßig wird für jeden im Azure-Portal erstellten virtuellen Computer eine neue NSG erstellt. Wenn Sie dieses Standardverhalten zulassen, werden Ihnen die NSGs schnell ausgehen. Denken Sie beim Entwurf an dieses Limit, und teilen Sie Ihre Ressourcen gegebenenfalls auf mehrere Regionen oder Abonnements auf. |
|NSG-Regeln pro NSG|200|Verwenden Sie einen breiten Bereich von IP-Adressen und Ports, um dieses Limit nicht zu überschreiten. |

>[AZURE.IMPORTANT] Sehen Sie sich alle [Einschränkungen an, die es hinsichtlich der Netzwerkdienste in Azure gibt](../azure-subscription-service-limits.md#networking-limits), bevor Sie Ihre Lösung entwerfen. Einige Einschränkungen können durch Öffnen eines Supporttickets erhöht werden.

### Entwurf von VNET und Subnetz

Da NSGs auf Subnetze angewendet werden können, können Sie die Anzahl der NSGs minimieren, indem Sie Ihre Ressourcen nach Subnetz gruppieren und die NSGs auf Subnetze anwenden. Wenn Sie sich dafür entscheiden, NSGs auf Subnetze anzuwenden, werden Sie u. U. feststellen, dass beim Definieren der vorhandenen VNETs und Subnetze die NSGs nicht bedacht wurden. Möglicherweise müssen Sie neue VNETs und Subnetze definieren, die Ihr NSG-Design unterstützen. Außerdem müssen Sie in den neuen Subnetzen neue Ressourcen bereitstellen. Anschließend können Sie in einer Migrationsstrategie festlegen, wie die vorhandenen Ressourcen in die neuen Subnetze verlagert werden.

### Spezielle Regeln

Sie müssen die folgenden besonderen Regeln berücksichtigen. Vergewissern Sie sich, dass Sie keinen Datenverkehr blockieren, der durch diese Regeln zugelassen wird. Andernfalls hat Ihre Infrastruktur keine Möglichkeit, mit wesentlichen Azure-Diensten zu kommunizieren.

- **Virtuelle IP des Hostknotens**: Grundlegende Infrastrukturdienste wie DHCP, DNS und die Systemüberwachung werden über die virtualisierte Host-IP-Adresse 168.63.129.16 bereitgestellt. Diese öffentliche IP-Adresse gehört Microsoft und ist die einzige virtuelle IP-Adresse, die in allen Regionen zu diesem Zweck verwendet wird. Die IP-Adresse wird der physischen IP-Adresse des Servercomputers (Hostknoten) zugeordnet, der den virtuellen Computer hostet. Der Hostknoten fungiert als DHCP-Relay, als rekursiver DNS-Resolver und als Integritätstestquelle für den Load Balancer und den Computer. Eingehende Kommunikation für diese IP-Adresse darf nicht als Angriff betrachtet werden.

- **Lizenzierung (Schlüsselverwaltungsdienst)**: Die auf den virtuellen Computern verwendeten Windows-Images müssen lizenziert werden. Zu diesem Zweck wird eine Lizenzierungsanforderung an die Hostserver des Schlüsselverwaltungsdiensts gesendet, die solche Abfragen verarbeiten. Dafür wird immer der ausgehende Port 1688 verwendet.

### ICMP-Datenverkehr

In NSG-Regeln kann derzeit als Protokoll nur *TCP* oder *UDP* angegeben werden. Ein spezielles Tag für *ICMP* ist nicht verfügbar. ICMP-Datenverkehr ist in einem virtuellen Netzwerk jedoch standardmäßig über die eingehende VNet-Regel (Standardregel 65500 eingehend) zulässig, die ein-/ausgehenden Datenverkehr für sämtliche Ports und Protokolle innerhalb des VNets zulassen.

### Subnetze

- Bedenken Sie die Anzahl der Ebenen, die für Ihre Workload erforderlich sind. Jede Ebene kann mithilfe eines Subnetzes isoliert werden, auf das eine NSG angewendet wird. 
- Wenn Sie ein Subnetz für ein VPN-Gateway oder eine ExpressRoute-Verbindung implementieren müssen, dürfen Sie auf dieses Subnetz **KEINE** NSG anwenden. Wenn Sie dies dennoch tun, funktionieren die Verbindungen zwischen VNETs oder zwischen Standorten nicht.
- Wenn Sie ein virtuelles Gerät implementieren müssen, müssen Sie das virtuelle Gerät in seinem eigenen Subnetz bereitstellen, damit die benutzerdefinierten Routen (UDRs, User Defined Routes) ordnungsgemäß funktionieren. Sie können eine NSG auf Subnetzebene implementieren, um den bei diesem Subnetz eingehenden und ausgehenden Datenverkehr zu filtern. Informieren Sie sich ausführlicher über das [Steuern des Datenverkehrsflusses und das Verwenden virtueller Geräte](virtual-networks-udr-overview.md).

### Load Balancer

- Berücksichtigen Sie die Lastenausgleichs- und NAT-Regeln für jeden Load Balancer, der von Ihren Workloads verwendet wird. Diese Regeln sind an einen Back-End-Pool gebunden, der Netzwerkkarten (bei Ressourcen-Manager-Bereitstellungen) oder virtuelle Computer/Rolleninstanzen (bei klassischen Bereitstellungen) enthält. Ziehen Sie in Erwägung, eine NSG für jeden Back-End-Pool zu erstellen und dadurch nur den Datenverkehr zuzulassen, der den in den Load Balancern implementierten Regeln entspricht. Dies garantiert, dass der direkt durch den Back-End-Pool kommende und den Load Balancer nicht passierende Datenverkehr ebenfalls gefiltert wird.
- In klassischen Bereitstellungen erstellen Sie Endpunkte, die die Ports auf einem Load Balancer zu Ports auf den virtuellen Computern oder Rolleninstanzen zuordnen. In einer Ressourcen-Manager-Bereitstellung können Sie auch eigene öffentlich zugängliche Load Balancer erstellen. Wenn Sie den Datenverkehr zu virtuellen Computern und Rolleninstanzen, die Teil eines Back-End-Pools in einem Load Balancer sind, mit NSGs einschränken, müssen Sie Folgendes bedenken: Der Zielport für den eingehenden Datenverkehr ist der tatsächliche Port im virtuellen Computer oder in der Rolleninstanz und nicht der Port, der durch den Load Balancer verfügbar gemacht wird. Bedenken Sie außerdem, das der Quellport und die Quelladresse für die Verbindung mit dem virtuellen Computer ein Port und eine Adresse auf dem Remotecomputer im Internet sind und nicht der Port und die Adresse, die vom Load Balancer verfügbar gemacht wird.
- Wenn Sie NSGs zum Filtern von Datenverkehr erstellen, der durch einen internen Load Balancer (ILB) geleitet wird, gilt Ähnliches wie bei öffentlich zugänglichen Load Balancern: Sie müssen berücksichtigen, dass der Quellport und der Quelladressbereich des Computers angewendet werden, von dem der Aufruf stammt, und nicht Port und Adresse des Load Balancers. Und der Zielport und der Zieladressbereich beziehen sich auf den Computer, bei dem der Datenverkehr eingeht, und nicht auf den Load Balancer.

### Andere

- Endpunktbasierte ACLs und NSGs können nicht für die gleiche VM-Instanz verwendet werden. Wenn Sie eine NSG verwenden möchten und bereits eine Endpunkt-ACL eingerichtet ist, entfernen Sie zuerst die Endpunkt-ACL. Informationen über die Vorgehensweise finden Sie unter [Verwalten von Endpunkt-ACLs](virtual-networks-acl-powershell.md).
- Im Bereitstellungsmodell mit Ressourcen-Manager können Sie eine NSG, die einer Netzwerkkarte zugeordnet ist, für virtuelle Computer mit mehreren Netzwerkkarten verwenden. Auf diese Weise wird eine Verwaltung (ein Remotezugriff) nach Netzwerkkarte und eine Trennung des Datenverkehrs ermöglicht.
- Beim Filtern von Datenverkehr aus anderen VNETs müssen Sie ähnlich wie bei Load Balancern den Quelladressbereich des Remotecomputers verwenden und nicht das Gateway, das die VNETs verbindet.
- Viele Azure-Dienste können nicht mit Azure Virtual Networks verbunden werden. Daher kann der ein- und ausgehende Datenverkehr dieser Dienste nicht mit NSGs gefiltert werden. Lesen Sie die Dokumentation zu den von Ihnen genutzten Diensten, um herauszufinden, ob sie mit VNETs verbunden werden können.

## Beispielbereitstellung

Im Folgenden wird veranschaulicht, wie die Informationen in diesem Artikel praktisch umgesetzt werden können. Dazu definieren wir NSGs, die den Netzwerk-Datenverkehr für eine Workloadlösung mit zwei Ebenen filtern. Es gelten diese Anforderungen:

1. Trennung des Datenverkehrs zwischen Front-End (Windows-Webserver) und Back-End (SQL-Datenbankserver).
2. Lastenausgleichsregeln leiten den am Load Balancer eingehenden Datenverkehr auf allen Webservern an Port 80 weiter.
3. NAT-Regeln leiten den an Port 50001 des Load Balancers eingehenden Datenverkehr an Port 3389 auf nur einem virtuellen Computer im Front-End weiter.
4. Kein Zugriff auf die virtuellen Computer des Front-Ends oder Back-Ends aus dem Internet, mit Ausnahme von Anforderung 1.
5. Kein Zugriff aus dem Front-End oder Back-End auf das Internet.
6. Zugriff auf Port 3389 auf jedem Webserver im Front-End für Datenverkehr, der aus dem Front-End-Subnetz selbst stammt.
7. Zugriff auf Port 3389 auf allen virtuellen SQL Server-Computern im Back-End nur für Datenverkehr aus dem Front-End-Subnetz.
8. Zugriff auf Port 1433 auf allen virtuellen SQL Server-Computern im Back-End nur für Datenverkehr aus dem Front-End-Subnetz.
9. Trennung des Verwaltungsdatenverkehrs (Port 3389) und des Datenbank-Datenverkehrs (Port 1433) auf verschiedene Netzwerkkarten der virtuellen Computer im Back-End.

![NSGs](./media/virtual-network-nsg-overview/figure1.png)

Wie im Diagramm oben zu sehen, sind die virtuellen Maschinen *Web1* und *Web2* mit dem Subnetz *FrontEnd* verbunden, und die virtuellen Maschinen *DB1* und *DB2* sind mit dem Subnetz *BackEnd* verbunden. Beide Subnetze sind Teil des VNETs *TestVNet*. Alle Ressourcen sind der Azure-Region *USA, Westen* zugeordnet.

Die Anforderungen 1–6 (außer 3) beschränken sich alle auf Subnetzräume. Die Anzahl der für die einzelnen NSGs nötigen Regeln soll möglichst gering sein, und es soll einfach sein, den Subnetzen weitere virtuelle Computer hinzuzufügen, die die gleichen Workloadarten ausführen. Deshalb werden die folgenden NSGs auf Subnetzebene implementiert.

### NSG für das Subnetz „FrontEnd“

**Regeln für eingehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|HTTP zulassen|Zulassen|100|INTERNET|*|*|80|TCP|
|RDP von FrontEnd zulassen|Zulassen|200|192\.168.1.0/24|*|*|3389|TCP|
|Alles aus dem Internet verweigern|Verweigern|300|INTERNET|*|*|*|TCP|

**Regeln für ausgehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|Internet verweigern|Verweigern|100|*|*|INTERNET|*|*|

### NSG für das Subnetz „BackEnd“

**Regeln für eingehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|Internet verweigern|Verweigern|100|INTERNET|*|*|*|*|

**Regeln für ausgehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|Internet verweigern|Verweigern|100|*|*|INTERNET|*|*|

### NSG für einen einzigen virtuellen Computer (Netzwerkkarte) in „FrontEnd“ für RDP aus dem Internet

**Regeln für eingehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|RDP aus dem Internet zulassen|Zulassen|100|INTERNET|*|\*|3389|TCP|

>[AZURE.NOTE] Beachten Sie, dass der Quelladressbereich für dieser Regel **Internet** ist und nicht die VIP des Load Balancers; der Quellport ist **\*** und nicht 500001. Verwechseln Sie NAT-Regeln und Lastenausgleichsregeln nicht mit NSG-Regeln. Die NSG-Regeln beziehen sich immer auf die ursprüngliche Quelle und das endgültige Ziel des Datenverkehrs, **NICHT** auf den zwischen beiden liegenden Load Balancer.

### NSG für Verwaltungsnetzwerkkarten in „BackEnd“

**Regeln für eingehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|RDP von FrontEnd zulassen|Zulassen|100|192\.168.1.0/24|*|\*|3389|TCP|

### NSG für die Datenbankzugriffs-Netzwerkkarten in „BackEnd“

**Regeln für eingehenden Datenverkehr**

|Regel|Access|Priority|Quelladressbereich|Quellport|Zieladressbereich|Zielport|Protokoll|
|---|---|---|---|---|---|---|---|
|SQL von FrontEnd zulassen|Zulassen|100|192\.168.1.0/24|*|\*|1433|TCP|

Da einige der oben aufgeführten NSGs einzelnen Netzwerkkarten zugeordnet werden müssen, müssen Sie dieses Szenario als Ressourcen-Manager-Bereitstellung bereitstellen. Beachten Sie, wie der Regeln für die Subnetz- und Netzwerkkartenebene je nach Notwendigkeit kombiniert werden.

## Nächste Schritte

- [Bereitstellen von NSGs im klassischen Bereitstellungsmodell](virtual-networks-create-nsg-classic-ps.md)
- [Bereitstellen von NSGs im Ressourcen-Manager](virtual-networks-create-nsg-arm-pportal.md)
- [Verwalten von NSG-Protokollen](virtual-network-nsg-manage-log.md)

[green]: ./media/virtual-network-nsg-overview/green.png
[yellow]: ./media/virtual-network-nsg-overview/yellow.png
[red]: ./media/virtual-network-nsg-overview/red.png

<!---HONumber=AcomDC_0323_2016-->