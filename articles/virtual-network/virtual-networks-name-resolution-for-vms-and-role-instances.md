<properties 
   pageTitle="Auflösung für virtuelle Computer und Rolleninstanzen"
	description="Szenarien für die Namensauflösung für Azure IaaS, Hybridlösungen, zwischen verschiedenen Clouddiensten, Active Directory und Verwenden von eigenen DNS-Servern"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/10/2015"
	ms.author="joaoma"/>

# Namensauflösung für virtuelle Computer und Rolleninstanzen

Abhängig davon, wie Sie Azure zum Hosten von IaaS, PaaS und Hybridlösungen verwenden, müssen Sie für die virtuellen Computer und Rolleninstanzen, die Sie erstellen, die Kommunikation untereinander zulassen. Obwohl diese Kommunikation über IP-Adressen erfolgen kann, ist es deutlich einfacher, Namen zu verwenden, die leicht zu merken sind und sich nicht verändern.

Wenn in Azure gehostete Rolleninstanzen und virtuelle Computer Domänennamen in interne IP-Adressen auflösen müssen, können sie eine dieser beiden Methoden verwenden:

- [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)

- [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server)

Welche Art der Namensauflösung Sie verwenden, hängt davon ab, wie die virtuellen Computer und Rolleninstanzen miteinander kommunizieren müssen.

**In der folgende Tabelle werden die Szenarien und entsprechenden Lösungen für die Namensauflösung dargestellt:**

| **Szenario** | **Namensauflösung bereitgestellt von:** | **Weitere Informationen finden Sie unter:** |
|--------------|----------------------------------|-------------------------------|
| Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern im gleichen Clouddienst | Von Azure bereitgestellte Namensauflösung | [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)|
| Namensauflösung zwischen virtuellen Computern und Rolleninstanzen im gleichen virtuellen Netzwerk | Von Azure bereitgestellte Namensauflösung (nur für ARM-basierte Bereitstellungen) oder Namensauflösung mithilfe eines eigenen DNS-Servers | [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution), [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements) |
| Namensauflösung zwischen virtuellen Computern und Rolleninstanzen in unterschiedlichen virtuellen Netzwerken | Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Standortübergreifend: Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern in Azure und lokalen Computern| Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Reverse-Lookup von internen IP-Adressen| Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Namensauflösung für nicht-öffentliche Domänen (z. B. Active Directory-Domänen)| Namensauflösung mithilfe eines eigenen DNS-Servers| [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) und [Anforderungen für DNS-Server](#dns-server-requirements)|
| Namensauflösung zwischen Rolleninstanzen in unterschiedlichen Clouddiensten, nicht in einem virtuellen Netzwerk| Nicht zutreffend Die Konnektivität zwischen virtuellen Computern und Rolleninstanzen in verschiedenen Clouddiensten wird außerhalb eines virtuellen Netzwerks nicht unterstützt.| Nicht zutreffend|


## Von Azure bereitgestellte Namensauflösung

Zusammen mit der Auflösung des öffentlichen DNS-Namens bietet Azure die Auflösung interner Namen für virtuelle Computer und Rolleninstanzen, die sich innerhalb des gleichen virtuellen Netzwerks oder Clouddienstes befinden. Virtuelle Computer oder Instanzen in einem Clouddienst teilen das gleiche DNS-Suffix, sodass der Hostname allein ausreichend ist. In klassischen virtuellen Netzwerken haben verschiedene Clouddienste verschiedene DNS-Suffixe, sodass der FQDN erforderlich ist. In ARM-basierten virtuellen Netzwerken ist das DNS-Suffix im ganzen virtuellen Netzwerk gleich, sodass der FQDN nicht erforderlich ist, und der DNS-Name entweder der Netzwerkkarte oder dem virtuellen Computer zugewiesen werden kann. Obwohl für die von Azure bereitgestellte Namensauflösung keine Konfiguration erforderlich ist, ist sie nicht für alle Bereitstellungsszenarien die beste Lösung, wie in der Tabelle oben dargestellt.

> [AZURE.NOTE]Im Fall von Web- und Workerrollen können Sie auf die internen IP-Adressen von Rolleninstanzen auf Grundlage des Rollennamens und der Instanznummer über die REST-API für die Azure-Service-Verwaltung zugreifen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funktionen und Überlegungen

**Funktionen:**

- Benutzerfreundlichkeit: Es ist keine Konfiguration erforderlich, um die von Azure bereitgestellte Namensauflösung zu verwenden.

- Die Namensauflösung wird zwischen Rolleninstanzen oder virtuellen Computern im gleichen Clouddienst bereitgestellt, ohne dass ein FQDN erforderlich ist.

- Die Namensauflösung wird zwischen virtuellen Computern in ARM-basierten virtuellen Netzwerken ohne Notwendigkeit des FQDN bereitgestellt. In klassischen Netzwerken ist der FQDN beim Auflösen von Namen in unterschiedlichen Clouddiensten erforderlich.

- Sie können die Hostnamen erstellen, die Ihre Bereitstellungen am besten beschreiben, und müssen nicht mit automatisch generierten Namen arbeiten.

**Überlegungen:**

- Die Namensauflösung zwischen virtuellen Netzwerken ist nicht verfügbar.

- Sie können nur Hostnamen für virtuelle Computer und Rolleninstanzen registrieren, die sich unter den ersten 180 Clouddiensten befinden, die einem virtuellen Azure-Netzwerk hinzugefügt wurden. Wenn Sie über mehr als 180 Clouddienste verfügen, müssen Sie unabhängig von der Anzahl von virtuellen Computern und Rolleninstanzen in jedem Dienst eigene DNS-Server für die Namensauflösung bereitstellen.

- Die standortübergreifende Namensauflösung ist nicht verfügbar.

- Das von Azure erstellte DNS-Suffix kann nicht geändert werden.

- Sie können keine eigenen Einträge manuell registrieren.

- WINS und NetBIOS werden nicht unterstützt. (Sie können Ihre virtuellen Computer nicht im Netzwerkbrowser in Windows Explorer auflisten.)

- Hostnamen müssen DNS-kompatibel sein. (Es dürfen nur 0-9, a-Z und "-" verwendet werden, und sie dürfen nicht mit "-" beginnen. Siehe RFC 3696 Abschnitt 2).

- Der DNS-Anfragedatenverkehr wird pro virtuellem Computer gedrosselt. Wenn Ihre Anwendung häufige DNS-Abfragen für mehrere Zielnamen ausführt, kann es bei einigen Abfragen zu Zeitüberschreitungen kommen. Um dies zu vermeiden, wird die Aktivierung der clientseitigen Zwischenspeicherung empfohlen. Auf Windows ist diese standardmäßig aktiviert, bei einigen Linux-Distributionen ist dies möglicherweise nicht der Fall.

## Namensauflösung mithilfe eines eigenen DNS-Servers

Wenn Ihre Anforderungen an die Namensauflösung über die von Azure bereitgestellten Funktionen hinausgehen, können Sie eigene DNS-Server verwenden. Wenn Sie eigene DNS-Server verwenden, sind Sie für die Verwaltung der DNS-Datensätze verantwortlich.

> [AZURE.NOTE]Es wird empfohlen, die Verwendung eines externen DNS-Servers zu vermeiden, wenn dies nicht aufgrund des Bereitstellungsszenarios erforderlich ist.

## Anforderungen für DNS-Server

Wenn Sie planen, eine nicht von Azure bereitgestellte Namensauflösung zu verwenden, muss der von Ihnen angegebene DNS-Server Folgendes unterstützen:

- Der DNS-Server sollte die dynamische DNS-Registrierung über das dynamische DNS (DDNS)-Protokoll akzeptieren, oder Sie müssen die erforderlichen Einträge erstellen.

- Falls auf dynamisches DNS zurückgegriffen wird, sollte beim DNS-Server der Aufräumvorgang für Datensätze deaktiviert sein. In Azure haben IP-Adressen einen langen DHCP-Lease, was dazu führen kann, dass Datensätze während des Aufräumvorgangs vom DNS-Server entfernt werden.

- Bei dem DNS-Server muss die Rekursion aktiviert werden, um die Auflösung von externen Domänennamen zu ermöglichen.

- Auf den DNS-Server muss von den Clients, die eine Namensauflösung anfordern, und den Diensten und virtuellen Computern, die ihre Namen registrieren, zugegriffen werden können (über TCP/UDP-Port 53).

- Es wird außerdem empfohlen, den DNS-Server gegenüber einem Zugriff aus dem Internet zu sichern, da viele Bots nach offenen rekursiven DNS-Resolvern suchen.

- Für eine optimale Leistung bei Verwendung von virtuellen Azure-Computern als DNS-Server sollte IPv6 deaktiviert und eine [öffentliche IP-Adresse auf Instanzebene](virtual-networks-instance-level-public-ip.mp) jedem virtuellen DNS-Servercomputer zugewiesen werden.

## Angeben von DNS-Servern

Sie können mehrere DNS-Server angeben, die von Ihren virtuellen Computern und Rolleninstanzen verwendet werden. Für jede DNS-Abfrage verwendet der Client zunächst den bevorzugten DNS-Server und verwendet die alternativen Server nur dann, wenn der bevorzugte Server nicht reagiert, d. h. wenn es keinen Lastenausgleich bei DNS-Abfragen über verschiedene DNS-Server hinweg gibt. Stellen Sie daher sicher, dass die DNS-Server in der richtigen Reihenfolge für Ihre Umgebung aufgeführt werden.

> [AZURE.NOTE]Wenn Sie die DNS-Einstellungen in einer Netzwerkkonfigurationsdatei für ein virtuelles Netzwerk ändern, das bereits bereitgestellt wurde, müssen Sie alle virtuellen Computer neu starten, damit die Änderungen wirksam werden.

### Angeben eines DNS-Servers im Verwaltungsportal

Beim Erstellen eines virtuellen Netzwerks im Verwaltungsportal können Sie die IP-Adresse und den Namen des DNS-Servers (oder der Server) angeben, die Sie verwenden möchten. Nach dem Erstellen des virtuellen Netzwerks werden die virtuellen Computer und Rolleninstanzen, die Sie im virtuellen Netzwerk bereitstellen, automatisch mit den angegebenen DNS-Einstellungen konfiguriert. DNS-Server, die für einen bestimmten Clouddienst (klassisches Azure) oder eine Netzwerkschnittstellenkarte (ARM-basierte Bereitstellungen) angegeben werden, haben Vorrang vor denen für das virtuelle Netzwerk. Siehe [Informationen zum Konfigurieren eines virtuellen Netzwerks im Verwaltungsportal](virtual-networks-settings.md).

### Angeben eines DNS-Servers mithilfe von Konfigurationsdateien (klassisches Azure)

Bei klassischen virtuellen Netzwerken können Sie DNS-Einstellungen mithilfe von zwei verschiedenen Konfigurationsdateien angeben: mit der *Netzwerkkonfigurationsdatei* und der *Dienstkonfigurationsdatei*.

> [AZURE.NOTE]DNS-Server in der Dienstkonfigurationsdatei überschreiben die Einstellungen in der Netzwerkkonfigurationsdatei.
 
Die Netzwerk-Konfigurationsdatei beschreibt die virtuellen Netzwerke in Ihrem Abonnement. Wenn Sie einem Clouddienst in einem virtuellen Netzwerk Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Netzwerkkonfigurationsdatei auf jede Rolleninstanz oder jeden virtuellen Computer angewendet, sofern keine Clouddienst-spezifischen DNS-Server angegeben wurden.

Die Dienstkonfigurationsdatei wird für jeden Clouddienst erstellt, den Sie in Azure hinzufügen. Wenn Sie dem Clouddienst Rolleninstanzen oder virtuelle Computer hinzufügen, werden die DNS-Einstellungen aus der Dienstkonfigurationsdatei auf jede Rolleninstanz oder virtuellen Computer angewendet.



## Nächste Schritte

[Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710)

[Konfigurationsschema für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/jj157100)

[Informationen zum Konfigurieren von Einstellungen für virtuelle Netzwerke im Verwaltungsportal](virtual-networks-settings.md)

[Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](virtual-networks-using-network-configuration-file.md)

<!---HONumber=September15_HO1-->